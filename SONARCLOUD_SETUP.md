<!--
 Licensed to the Apache Software Foundation (ASF) under one or more
 contributor license agreements.  See the NOTICE file distributed with
 this work for additional information regarding copyright ownership.
 The ASF licenses this file to You under the Apache License, Version 2.0
 (the "License"); you may not use this file except in compliance with
 the License.  You may obtain a copy of the License at

      https://www.apache.org/licenses/LICENSE-2.0

 Unless required by applicable law or agreed to in writing, software
 distributed under the License is distributed on an "AS IS" BASIS,
 WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
 See the License for the specific language governing permissions and
 limitations under the License.
-->

# Configuração do SonarCloud para Apache Commons Lang

Este guia mostra como configurar e usar o SonarCloud para análise de cobertura de testes neste projeto.

## Configuração Inicial

### 1. Configurar o SonarCloud

1. Acesse https://sonarcloud.io
2. Faça login com sua conta GitHub
3. Clique em "+" → "Analyze new project"
4. Selecione o repositório do projeto
5. Anote a **Organization** e **Project Key** gerados

### 2. Configurar Secrets no GitHub

1. Vá para o repositório no GitHub
2. Acesse **Settings** → **Secrets and variables** → **Actions**
3. Clique em "New repository secret"
4. Adicione:
   - **Nome**: `SONAR_TOKEN`
   - **Valor**: Token gerado no SonarCloud (My Account → Security → Generate Token)

### 3. Configurações já aplicadas

As seguintes configurações já estão no `pom.xml` (linhas 173-177):

```xml
<!-- SonarCloud -->
<sonar.projectKey>commons-lang</sonar.projectKey>
<sonar.organization>gabrielsoaressn</sonar.organization>
<sonar.host.url>https://sonarcloud.io</sonar.host.url>
<sonar.coverage.jacoco.xmlReportPaths>${project.build.directory}/site/jacoco/jacoco.xml</sonar.coverage.jacoco.xmlReportPaths>
```

O workflow `.github/workflows/sonarcloud.yml` também já está configurado corretamente.

## Como Usar

### Executar Localmente

Para gerar o relatório de cobertura localmente:

```bash
# Gerar relatório de cobertura
mvn clean verify -Pcoverage

# Visualizar o relatório
open target/site/jacoco/index.html
```

### Enviar para o SonarCloud

```bash
# Executar análise completa e enviar para SonarCloud
mvn clean verify sonar:sonar \
  -Dsonar.token=SEU_TOKEN \
  -Pcoverage
```

**Nota**: Substitua `SEU_TOKEN` pelo token gerado no SonarCloud.

### Análise Automática via GitHub Actions

O workflow `.github/workflows/sonarcloud.yml` executa automaticamente:
- Em todo push para a branch `master`
- Em todo pull request

## Visualizar Resultados

1. Acesse https://sonarcloud.io/project/overview?id=commons-lang
2. Ou navegue manualmente:
   - Acesse https://sonarcloud.io
   - Selecione seu projeto `commons-lang`
3. Visualize as métricas:
   - **Coverage**: Cobertura de testes
   - **Code Smells**: Problemas de qualidade
   - **Bugs**: Possíveis bugs
   - **Security Hotspots**: Problemas de segurança
   - **Duplications**: Código duplicado
   - **Maintainability**: Índice de manutenibilidade

## Configurações do JaCoCo

O projeto já está configurado com JaCoCo através do `commons-parent`. Os thresholds atuais são:

- Class Coverage: 99%
- Instruction Coverage: 96%
- Method Coverage: 96%
- Branch Coverage: 92%
- Line Coverage: 96%
- Complexity Coverage: 91%

Você pode ver essas configurações no `pom.xml` nas linhas 164-171.

## Arquivos Criados/Modificados

1. `.github/workflows/sonarcloud.yml` - Workflow do GitHub Actions
2. `pom.xml` - Adicionado profile `coverage` para JaCoCo
3. Este arquivo de documentação

## Troubleshooting

### Erro: "Please provide compiled classes"

Certifique-se de executar `mvn clean verify` antes de `sonar:sonar`.

### Erro: "Invalid token"

Verifique se o `SONAR_TOKEN` está configurado corretamente nos secrets do GitHub.

### Cobertura não aparece no SonarCloud

Certifique-se de que:
1. O profile `coverage` está sendo usado: `-Pcoverage`
2. Os testes foram executados: `verify` antes de `sonar:sonar`
3. O caminho do relatório está correto na propriedade `sonar.coverage.jacoco.xmlReportPaths`

## Links Úteis

- [Documentação do SonarCloud](https://docs.sonarcloud.io/)
- [JaCoCo Maven Plugin](https://www.jacoco.org/jacoco/trunk/doc/maven.html)
- [SonarScanner for Maven](https://docs.sonarcloud.io/advanced-setup/ci-based-analysis/sonarscanner-for-maven/)
