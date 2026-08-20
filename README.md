# shared-actions

Workflows reutilizáveis do GitHub Actions para padronizar a automação dos meus
projetos.

## Workflows disponíveis

### Java CI

Arquivo: `.github/workflows/java-ci.yml`

Workflow reutilizável para projetos Java com Maven. Ele:

- usa o JDK 21 (Temurin);
- habilita o cache de dependências do Maven;
- executa `mvn clean test`.

Para utilizá-lo em outro repositório, crie um workflow chamador:

```yaml
name: CI

on:
	push:
	pull_request:

jobs:
	java-ci:
		uses: SEU_USUARIO/shared-actions/.github/workflows/java-ci.yml@main
```

### Gemini PR Description

Arquivo: `.github/workflows/gemini-analisys-pr.yml`

Gera automaticamente uma descrição em Markdown para Pull Requests a partir do
diff da alteração. O workflow:

1. obtém o diff completo da Pull Request;
2. limita o conteúdo enviado à API a 20.000 caracteres;
3. tenta gerar um resumo usando o Gemini;
4. tenta um modelo alternativo caso o primeiro falhe;
5. atualiza a descrição da Pull Request com o resultado.

Esse workflow exige o segredo `GEMINI_API_KEY` no repositório que o chamar:

```yaml
name: Descrição da Pull Request

on:
	pull_request:
		types: [opened, synchronize, reopened]

jobs:
	generate-description:
		uses: SEU_USUARIO/shared-actions/.github/workflows/gemini-analisys-pr.yml@main
		secrets:
			GEMINI_API_KEY: ${{ secrets.GEMINI_API_KEY }}
```

## Como adicionar um workflow

1. Crie um arquivo `.yml` em `.github/workflows/`.
2. Use `on.workflow_call` para permitir que outros repositórios o reutilizem.
3. Declare explicitamente os `inputs` e `secrets` necessários.
4. Mantenha as versões das actions fixadas e documente o comportamento neste README.

## Requisitos

- Os workflows são executados em runners Ubuntu hospedados pelo GitHub.
- O repositório chamador deve permitir o uso de workflows reutilizáveis.
- O workflow do Gemini precisa de uma chave válida da API Gemini configurada como segredo.
