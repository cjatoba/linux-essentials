## Copiando um arquivo remoto para máquina local:
```bash
scp user@domain:/pasta-remota/arquivo-remoto.txt /pasta-local/arquivo-local.txt
```

## Enviando um arquivo local para um servidor remoto:

```bash
scp /pasta-local/arquivo-local.txt user@domain:/pasta-remota/arquivo-remoto.txt
```

## Copiando pastas e subpastas do servidor remoto para máquina local:

```bash
scp -r user@domain:/pasta-remota/ /pasta-local/
```

## Enviando pastas e subpastas da máquina local para o servidor remoto:

```bash
scp -r /pasta-local/ user@domain:/pasta-remota/
```

## Enviando pastas e subpastas de máquina remota para um servidor remoto:

```bash
scp -r user@domain:/pasta-remota user@domain:/pasta-remota/
```
