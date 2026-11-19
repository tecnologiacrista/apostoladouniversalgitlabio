# apostoladouniversalgitlabio

Publica o redirect `https://apostoladouniversal.gitlab.io/` → `https://www.apostoladouniversal.com/` em GitLab Pages.

## Estrutura

```
apostoladouniversalgitlabio/
├── site/
│   ├── index.html      <- redireciona para https://www.apostoladouniversal.com/
│   ├── 404.html        <- idem, para qualquer URL inexistente
│   └── .gitlab-ci.yml  <- job "pages" (obrigatório no GitLab Pages)
├── deploy.sh           <- script de publicação
├── LICENSE             <- MPL-2.0
├── .gitignore          <- ignora .deploy/
└── README.md
```

## Como usar

```bash
cd apostoladouniversalgitlabio
./deploy.sh
```

Destino: `https://apostoladouniversal.gitlab.io/`.

## O que o script faz

1. Cria um repositório git **novo e vazio** (sem histórico) usando `site/` como work-tree.
2. Faz **um único commit** com todo o conteúdo.
3. `git push --force` → o histórico do remoto passa a ser **esse único commit**.

> Não copia arquivos (usa `site/` direto), então funciona mesmo com gigabytes.
> Um `.gitignore` dentro de `site/`, se existir, é respeitado.

## Token

Lido de `$KEYS/apostoladouniversal_gitlabio.txt` — pasta dos tokens, padrão
`${EXT42020ENV:-$HOME/EXT42020ENV}/KEYS` (defina `KEYS` para outro lugar).
Formato da URL: `https://oauth2:<TOKEN>@gitlab.com/...`

## Variáveis opcionais

```bash
CONTENT=/outra/pasta      ./deploy.sh   # conteúdo de outra pasta
KEYS=/outra/keys          ./deploy.sh   # outra pasta de tokens
MESSAGE="texto"           ./deploy.sh   # mensagem do commit
GITLAB_REMOTE=/tmp/x.git  ./deploy.sh   # remoto alternativo (teste)
```

## Observações

- **Isso apaga o histórico remoto de forma irreversível.**
- O redirect usa `<link rel="canonical">`, `<meta http-equiv="refresh">` e
  `location.replace()` — três caminhos, para funcionar mesmo com JS desligado.
- O GitLab precisa do `.gitlab-ci.yml` com o job `pages` (já incluído em
  `site/`); sem ele o Pages responde “Missing CI config file”.
- A branch `main` precisa estar **desprotegida** para o force push; se for
  reprotegida, o `deploy.sh` volta a falhar.
- Depois do push, o pipeline roda sozinho (~30–60 s) e atualiza o site.

## Licença

Todo o conteúdo deste repositório — `deploy.sh`, `site/` e esta documentação —
é **MPL-2.0** ([`LICENSE`](LICENSE)).

Nenhum arquivo de terceiros é redistribuído aqui. O token não está neste
repositório: é lido de `$KEYS` em tempo de execução.
