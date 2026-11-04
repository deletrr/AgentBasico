# Agente PC

Agente local que controla o Windows, pesquisa na internet e guarda o que aprende. Roda em cima do Ollama, então tudo fica na sua máquina. A interface é uma janela comum, sem terminal.

## O que ele faz

- Abre programas, arquivos, pastas e sites.
- Executa comandos PowerShell.
- Lê e escreve arquivos.
- Pesquisa na web, lê páginas e baixa arquivos.
- Olha a tela e opera mouse e teclado.
- Salva lições em um arquivo de memória e cria novas ferramentas para si mesmo (com a sua aprovação).

## Requisitos

- Windows 10 ou 11
- Python 3.10 ou superior (marque "Add Python to PATH" ao instalar)
- Ollama instalado e rodando: https://ollama.com/download
- Cerca de 16 GB de RAM para o modelo de 14B. Com menos, use um modelo menor (veja a seção sobre trocar o modelo).

## Instalação

Coloque `agente.py` e `requirements.txt` na mesma pasta e abra um terminal nela.

1. Crie e ative um ambiente virtual (opcional, mas recomendado):

```
python -m venv .venv
.venv\Scripts\activate
```

2. Instale as dependências:

```
pip install -r requirements.txt
```

3. Baixe os modelos no Ollama:

```
ollama pull qwen2.5:14b
ollama pull qwen2.5vl:7b
```

O primeiro é o modelo principal. O segundo só é usado para olhar a tela; sem ele o agente funciona normalmente, apenas a leitura de tela falha.

## Como rodar

Com o Ollama aberto (ele fica na bandeja do sistema):

```
python agente.py
```

Para abrir sem a janela preta do terminal, use `pythonw agente.py`.

## Uso

Escreva o pedido na caixa de baixo e aperte Enter (Shift+Enter quebra a linha). Exemplos:

- abra o bloco de notas e escreva uma lista de compras
- pesquise os próximos jogos do Palmeiras
- liste os arquivos da minha pasta Downloads
- abra o WhatsApp

Controles na lateral:

- **Modo autônomo:** desligado, o agente pede confirmação antes de rodar comandos, gravar arquivos, baixar coisas e usar mouse e teclado. Ligado, ele executa sem perguntar. Ações perigosas (apagar, instalar, desligar, alterar o próprio código, criar ferramentas) pedem confirmação nos dois modos.
- **Parar:** interrompe a tarefa atual.
- **Nova conversa:** limpa o histórico da sessão.
- **Recarregar ferramentas:** relê a pasta de plugins.

Se o agente perder o controle do mouse, jogue o cursor no canto superior esquerdo da tela. Isso aborta a ação.

## Onde ficam os dados

Tudo é criado em `agente_dados`, ao lado do `agente.py`:

- `memoria.md`: lições salvas, lidas em toda conversa. Pode editar à mão.
- `plugins/`: ferramentas criadas pelo agente. Apague o arquivo para remover.
- `log.txt`: histórico de comandos e ações.
- `tela.png`: última captura de tela.

## Trocar o modelo

Os modelos são definidos no começo do `agente.py`, na seção CONFIGURACAO:

```python
MODELO_TEXTO: str = "qwen2.5:14b"
MODELO_VISAO: str = "qwen2.5vl:7b"
NUM_CTX: int = 16384
```

Passos:

1. Baixe o modelo novo: `ollama pull nome:tag`
2. Veja o nome exato com `ollama list`
3. Troque o valor de `MODELO_TEXTO` (ou `MODELO_VISAO`) pelo nome que apareceu
4. Feche e abra o agente de novo

Observações:

- O modelo de texto precisa suportar chamada de ferramentas (tool calling). Na página do modelo no site do Ollama, procure a etiqueta "tools". Sem isso o agente só conversa e não executa nada.
- O modelo de visão precisa aceitar imagens (etiqueta "vision").
- Para PCs mais fracos, `qwen2.5:7b` ou `qwen2.5:3b` são alternativas, com menos precisão em tarefas de vários passos.
- Se faltar memória, reduza `NUM_CTX` para 8192.

## Problemas comuns

**Erro de conexão com o Ollama:** o Ollama não está aberto. Abra o aplicativo ou rode `ollama serve`.

**Model not found:** o nome em `agente.py` não bate com o de `ollama list`, ou o `ollama pull` não foi feito.

**O agente responde mas não faz nada:** o modelo não suporta ferramentas, ou é pequeno demais. Volte para um modelo com tag "tools" e de preferência 14B ou maior.

**Erro de módulo (ModuleNotFoundError):** rode `pip install -r requirements.txt` com o mesmo Python que executa o `agente.py`.

**Cliques no lugar errado:** localizar elementos por imagem com um modelo de 7B é impreciso. O agente prioriza atalhos de teclado e comandos; se precisar de cliques confiáveis, use um modelo de visão maior.

**Pesquisa web falha:** o DuckDuckGo limita muitas requisições seguidas. Espere um pouco e tente de novo.

## Segurança

O agente executa comandos reais no seu computador. Leia os pedidos de confirmação antes de aprovar, principalmente com comandos e código novo. Páginas da web e arquivos são tratados como dados, mas nenhum modelo local é imune a texto malicioso, então evite o modo autônomo em tarefas que leem conteúdo de fontes desconhecidas.
