# [Fix] Linux: áudio some após pausa ou troca de aplicativo — PipeWire suspende dispositivos ALSA automaticamente (WirePlumber suspend-timeout)

**Palavras-chave:** linux sem áudio, dispositivos de áudio desaparecem, nenhum dispositivo de áudio encontrado, pipewire suspend, wireplumber suspend-timeout, áudio para de funcionar linux, chrome sem áudio linux, teams sem áudio linux, áudio some após inatividade, wpctl status vazio, alsa suspended

---

## O problema

Em sistemas Linux com PipeWire, os dispositivos de áudio podem desaparecer das configurações do sistema e o áudio parar de funcionar após um período de inatividade. Isso pode ocorrer em diferentes situações:

- Pausar um vídeo no Chrome por alguns segundos e retomar
- Sair de uma reunião no Teams e entrar em outra
- Trocar de aba ou aplicativo enquanto áudio estava ativo
- Qualquer cenário onde o stream de áudio é interrompido brevemente

---

## Por que isso acontece

### A suspensão automática é comportamento padrão

A suspensão de dispositivos de áudio é um recurso **intencional e ativo por padrão** no WirePlumber (o gerenciador de sessão do PipeWire). Trata-se de uma medida de economia de energia: manter um dispositivo de áudio aberto consome CPU continuamente — o driver ALSA fica em loop processando buffers mesmo sem nenhum som sendo reproduzido. Em notebooks com bateria isso tem impacto real.

O WirePlumber monitora todos os nós de áudio e, após **5 segundos sem nenhum stream ativo**, envia um comando `Suspend` ao dispositivo, colocando o driver ALSA em estado de baixo consumo.

### Por que o áudio não restaura automaticamente

Em teoria, o PipeWire deveria acordar o dispositivo automaticamente quando um novo stream tentasse usá-lo. Na prática, isso falha em aplicações como Chrome e Teams por uma combinação de razões:

1. **Cache de estado corrompido:** quando o Chrome ou Teams conecta ao dispositivo de áudio, ele guarda internamente o estado daquela conexão. Quando o dispositivo é suspenso, a conexão não é encerrada formalmente — ela fica em estado corrompido. Ao retomar o vídeo ou entrar em uma nova reunião, a aplicação tenta usar a conexão antiga em vez de abrir uma nova.

2. **Camada de compatibilidade:** Chrome e Teams se comunicam com o PipeWire via API PulseAudio (através do `pipewire-pulse`). Essa camada de compatibilidade nem sempre propaga corretamente os eventos de suspend/resume para a aplicação, que então não sabe que precisa reconectar.

3. **Condição de corrida:** quando o usuário retoma o conteúdo, a aplicação envia áudio ao PipeWire antes que o dispositivo termine de acordar. Esse envio falha silenciosamente e a aplicação não tenta novamente.

### Por que os dispositivos desapareciam das configurações de áudio

A suspensão por si só não remove os dispositivos da lista. O que causava o desaparecimento era uma consequência em cascata:

1. Dispositivo entra em idle → WirePlumber suspende após 5 segundos
2. Chrome ou Teams tenta usar o áudio e falha ao comunicar com o dispositivo suspenso
3. O aplicativo começa a enviar erros repetidos para o `pipewire-pulse`
4. O `pipewire-pulse` entra em estado corrompido
5. O GNOME Configurações consulta os dispositivos via `pipewire-pulse` — que nesse estado responde com lista vazia
6. Resultado: nenhum dispositivo exibido nas configurações, mesmo com o PipeWire ainda rodando

Os dispositivos não desapareciam do PipeWire em si, mas a camada de compatibilidade que faz a ponte com o GNOME estava corrompida e não os reportava corretamente. Por isso reiniciar apenas o Chrome ou o Teams não resolvia — era necessário reiniciar os serviços de áudio ou o sistema.

---

## A solução

A correção desabilita a suspensão automática para todos os dispositivos ALSA via uma regra do WirePlumber, usando a propriedade correta que o sistema lê internamente.

> **Atenção:** a propriedade correta é `session.suspend-timeout-seconds`. Uma tentativa comum é usar `session.suspend-timeout` (sem o `-seconds`), mas o script interno do WirePlumber (`suspend-node.lua`) lê apenas a versão com sufixo — o valor é ignorado e o suspend continua ocorrendo.

---

## Pré-requisito: verificar se o sistema usa PipeWire + WirePlumber

```bash
systemctl --user status pipewire wireplumber
```

Se ambos aparecerem como `active (running)`, esta solução se aplica. Para verificar as versões instaladas:

```bash
pipewire --version
wireplumber --version
```

> Esta solução foi testada no WirePlumber 0.4.x. Em versões anteriores o mecanismo de suspend pode ser diferente.

---

## Passos para correção

### 1. Remover configs incorretos (se existirem)

```bash
rm -f ~/.config/pipewire/pipewire.conf.d/disable-suspend.conf
rm -f ~/.config/pipewire/pipewire.conf.d/usb-stable.conf
```

> **Atenção:** não coloque `libpipewire-module-protocol-pulse` em configs do processo principal do PipeWire — esse módulo já é carregado pelo `pipewire-pulse.service` e causa conflito de socket, derrubando o stack de áudio completamente.

### 2. Criar o config correto no WirePlumber

```bash
mkdir -p ~/.config/wireplumber/main.lua.d

cat > ~/.config/wireplumber/main.lua.d/51-disable-suspend.lua << 'EOF'
table.insert(alsa_monitor.rules, {
  matches = {
    {
      { "node.name", "matches", "alsa_*" },
    },
  },
  apply_properties = {
    ["session.suspend-timeout-seconds"] = 0,
  },
})
EOF
```

### 3. Reiniciar os serviços

```bash
systemctl --user reset-failed
systemctl --user restart pipewire pipewire-pulse wireplumber
```

### 4. Verificar que está funcionando

```bash
wpctl status
```

O comando deve listar os dispositivos de áudio normalmente:

```
Audio
 ├─ Devices:
 │      XX. Built-in Audio     [alsa]
 ├─ Sinks:
 │  *   XX. Built-in Audio Analog Stereo   [vol: 1.00]
```

Se a lista aparecer vazia, execute `journalctl --user -u wireplumber -n 30` para verificar erros.

### 5. Reiniciar aplicativos de áudio

Após aplicar a correção, reinicie o Chrome, Teams ou qualquer outro aplicativo de áudio — eles não reconectam ao dispositivo automaticamente se já estavam com o stream corrompido.

---

## Como a solução funciona

### O que o script Lua faz — linha a linha

```lua
table.insert(alsa_monitor.rules, {
```
`alsa_monitor.rules` é a lista de regras de comportamento do WirePlumber para dispositivos ALSA. O `table.insert` adiciona uma nova regra ao final dessa lista sem apagar as regras existentes do sistema.

```lua
  matches = {
    {
```
Define as condições para a regra ser aplicada. O nível duplo de chaves existe porque é possível ter múltiplos grupos de condições — se qualquer grupo bater, a regra é aplicada. Aqui há apenas um grupo.

```lua
      { "node.name", "matches", "alsa_*" },
```
A condição: três elementos — a propriedade a verificar (`node.name`), o operador (`matches`, que aceita padrão glob), e o valor (`alsa_*`). O asterisco é um curinga que representa qualquer texto. Isso aplica a regra a **todos os dispositivos de áudio ALSA da máquina**, sem precisar conhecer o nome do hardware específico.

```lua
  apply_properties = {
    ["session.suspend-timeout-seconds"] = 0,
  },
```
Define o que fazer nos nós que baterem com as condições: aplicar a propriedade `session.suspend-timeout-seconds` com valor `0`. É exatamente essa propriedade que o `suspend-node.lua` do WirePlumber consulta antes de suspender um dispositivo. Quando o valor é `0`, o script descarta a suspensão e não agenda nenhum timer.

### Quando o script é executado e como a configuração persiste

O script **não é executado uma única vez** — ele é carregado e executado pelo WirePlumber toda vez que o serviço inicia:

1. Sistema liga ou WirePlumber é reiniciado
2. WirePlumber carrega todos os arquivos `.lua` de `main.lua.d/` — tanto os do sistema (`/usr/share/wireplumber/main.lua.d/`) quanto os do usuário (`~/.config/wireplumber/main.lua.d/`)
3. O script é executado, inserindo a regra na lista `alsa_monitor.rules`
4. Sempre que um novo dispositivo ALSA é detectado, o WirePlumber percorre todas as regras e aplica as propriedades nos dispositivos que batem com as condições

A configuração **não é gravada no dispositivo nem no kernel** — ela existe apenas enquanto o WirePlumber está rodando. O que persiste é o arquivo `.lua` em `~/.config/wireplumber/main.lua.d/51-disable-suspend.lua`. Por estar nessa pasta, ele é carregado automaticamente em todo boot, tornando o comportamento permanente enquanto o arquivo existir.

Se o arquivo for removido e o WirePlumber reiniciado, o suspend voltaria a funcionar com o comportamento padrão de 5 segundos.
