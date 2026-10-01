# Bravocop Control Agent Releases

Distribuição pública dos instaladores do agente Bravocop Control.

## Versões e downloads

- [Última release estável](https://github.com/JPDovale/bravocop-control-agent-releases/releases/latest)
- [Todas as releases](https://github.com/JPDovale/bravocop-control-agent-releases/releases)
- Endpoint público para atualizadores: `https://api.github.com/repos/JPDovale/bravocop-control-agent-releases/releases/latest`.

A consulta e o download são públicos e não exigem token GitHub. O atualizador deve selecionar
os assets da versão e arquitetura corretas a partir da resposta do endpoint.

| Asset | Uso |
| --- | --- |
| `bravocop-agent-VERSION-linux-x64.run` | Instalador independente com Node e agente embutidos |
| `bravocop-agent-VERSION.mjs` | Instalador com agente embutido; exige Node >= 22.14 |
| `bravocop-control-agent-VERSION.tar.gz` | Pacote do agente com dependências de produção |
| `bravocop-agent-VERSION.sha256` | Checksums SHA-256 dos três artefatos |

A release inicial `v0.1.0` disponibiliza Linux x64. Não contém instalador arm64.
As releases publicadas são imutáveis; correções exigem uma versão e tag novas.

## Instalação e atualização

O `.run` exige Linux x64 com systemd, Docker via socket Unix, util-linux e glibc compatível.
Verifique o SHA-256 antes da execução. Cadastro da máquina e credencial são fornecidos
na primeira instalação; credenciais não fazem parte dos downloads.

```sh
sudo sh ./bravocop-agent-0.1.0-linux-x64.run install \
  --url https://control.seudominio.com \
  --machine-id UUID_DA_MAQUINA

sudo sh ./bravocop-agent-0.1.0-linux-x64.run update --latest
sudo sh ./bravocop-agent-0.1.0-linux-x64.run status
sudo sh ./bravocop-agent-0.1.0-linux-x64.run rollback
```

A primeira instalação solicita o token sem ecoar seu valor. Update e rollback preservam
configuração, credencial, fila em disco e o estado de ativação do serviço.

Se houver deploy, build, tarefa ou recuperação em andamento, a atualização retorna
`AGENT_UPDATE_DEFERRED` antes de parar o agente. O chamador deve tentar novamente depois.
Um agente antigo sem o protocolo de lock exige concluir seus fluxos e parar explicitamente
o serviço antes da primeira atualização protegida.

O comando `update --latest` consulta este repositório, valida a release estável e imutável,
origem, tamanho e SHA-256, e executa o instalador novo com o agente e runtime embutidos.
Não reinstala a mesma versão nem faz downgrade. Erros de download/validação não param o agente.
O agendamento periódico ainda exige um atualizador local separado; cada chamada realiza uma verificação.
