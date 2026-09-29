# Roteiro de Demonstração Prática — AWS CloudFormation
### Guia passo a passo para gravar o vídeo (Console + CLI)

Exemplo escolhido: criar (e depois remover) um **bucket S3** via CloudFormation.
Motivo da escolha: é grátis, é rápido de criar/destruir (poucos segundos), e é fácil de mostrar no console visualmente — ideal para caber nos 15-20 min da apresentação.

---

## 0. Pré-requisitos (preparar ANTES de gravar)

- [ ] Conta AWS ativa (Free Tier é suficiente)
- [ ] Usuário IAM com permissão de `CloudFormationFullAccess` e `AmazonS3FullAccess` (ou `AdministratorAccess` se for conta de estudo)
- [ ] Login já feito no console AWS, região definida (ex.: `us-east-1` ou `sa-east-1`)
- [ ] (Opcional, para a versão via terminal) AWS CLI instalado e configurado com `aws configure`
- [ ] O arquivo `template.yaml` abaixo salvo no computador, já aberto no editor de texto para mostrar em tela
- [ ] Testem o passo a passo pelo menos uma vez ANTES de gravar oficialmente — evita erro de permissão ou nome de bucket duplicado ao vivo

---

## 1. O template (mostrar o arquivo primeiro — ~1 min)

Abram o arquivo `template.yaml` e expliquem rapidamente cada linha na tela:

```yaml
AWSTemplateFormatVersion: '2010-09-09'
Description: Demonstração simples - bucket S3 criado via CloudFormation

Resources:
  MeuBucketDemo:
    Type: AWS::S3::Bucket
    Properties:
      BucketName: fatec-devops-demo-SEUNOMEAQUI
      Tags:
        - Key: Projeto
          Value: ApresentacaoDevOps

Outputs:
  NomeDoBucket:
    Description: Nome do bucket criado
    Value: !Ref MeuBucketDemo
```

**Fala sugerida:** "Esse é o template — a 'receita' que descreve o que queremos na AWS. `Resources` lista os recursos, `Type` diz que é um bucket S3, e `Outputs` mostra o que o CloudFormation vai devolver depois de criar."

> ⚠️ O nome do bucket (`BucketName`) precisa ser **único no mundo todo**. Troquem `SEUNOMEAQUI` por algo único (ex.: nome do grupo + data) antes de testar.

---

## 2. Criar a stack pelo Console AWS (~3-4 min)

1. No console AWS, busquem **CloudFormation** na barra de pesquisa
2. Cliquem em **Create stack** → **With new resources (standard)**
3. Em "Prerequisite", deixem **Choose an existing template**
4. Em "Specify template", selecionem **Upload a template file** → escolham o `template.yaml`
5. Cliquem **Next**
6. Deem um nome à stack, ex.: `demo-fatec-devops` → **Next**
7. Na tela de opções, deixem tudo padrão → **Next**
8. Revisem o resumo e cliquem **Submit**

**Fala sugerida:** "Agora a AWS vai ler o template e criar exatamente o que descrevemos."

---

## 3. Acompanhar o provisionamento ao vivo (~1-2 min)

1. Fiquem na aba **Events** da stack
2. Mostrem o status mudando: `CREATE_IN_PROGRESS` → `CREATE_COMPLETE`
3. Cliquem na aba **Resources** — mostrem o bucket listado ali, com link direto para ele
4. Cliquem na aba **Outputs** — mostrem o nome do bucket que o CloudFormation devolveu

**Fala sugerida:** "Vejam que não precisamos clicar em nada no S3 — o CloudFormation criou o recurso sozinho, seguindo exatamente o que estava no template."

---

## 4. Confirmar no serviço de destino (~1 min)

1. Abram o console do **S3** em outra aba
2. Mostrem o bucket criado, com o nome e a tag `Projeto: ApresentacaoDevOps`

**Fala sugerida:** "Esse bucket existe de verdade agora — e se precisássemos recriar esse mesmo ambiente em outra conta ou região, bastaria rodar o mesmo template."

---

## 5. (Opcional, se sobrar tempo) Demonstrar um Change Set — ~2 min

Mostra a "prévia de mudanças" antes de aplicar, um dos diferenciais do CloudFormation.

1. Editem o `template.yaml`: adicionem uma nova tag, ex.:
   ```yaml
        - Key: Ambiente
          Value: Demonstracao
   ```
2. No console CloudFormation, abram a stack → **Stack actions** → **Create change set for current stack**
3. Façam upload do template atualizado → **Next** até revisar
4. Mostrem a tela de **Changes**: ela lista exatamente o que vai mudar (a nova tag), sem aplicar ainda
5. Cliquem **Execute change set** para aplicar de fato

**Fala sugerida:** "Isso é o Change Set — o CloudFormation nos mostra o que vai mudar antes de mexer em produção, evitando surpresas."

---

## 6. Remover tudo (encerramento da demo — ~1 min)

1. Voltem para a stack no CloudFormation
2. **Stack actions** → **Delete stack** → confirmem
3. Acompanhem o status `DELETE_IN_PROGRESS` → a stack desaparece da lista
4. Mostrem rapidamente o console do S3: o bucket já não existe mais

**Fala sugerida:** "E com um único clique, tudo que criamos é desfeito automaticamente — sem sobrar nenhum recurso esquecido gerando custo."

---

## Alternativa via terminal (AWS CLI)

Se preferirem gravar pelo terminal em vez do console (mais "dev", mais rápido):

```bash
# 1. Criar/atualizar a stack a partir do template
aws cloudformation deploy \
  --template-file template.yaml \
  --stack-name demo-fatec-devops

# 2. Ver o status e os outputs
aws cloudformation describe-stacks \
  --stack-name demo-fatec-devops \
  --query "Stacks[0].[StackStatus,Outputs]"

# 3. Confirmar que o bucket existe
aws s3 ls | grep fatec-devops-demo

# 4. Remover tudo
aws cloudformation delete-stack \
  --stack-name demo-fatec-devops
```

**Dica de gravação:** rodem `aws cloudformation deploy` e deixem a tela do terminal visível — ele mostra o progresso em tempo real (`Waiting for stack create/update to complete`), o que fica bem didático no vídeo.

---

## Checklist de gravação

- [ ] Gravar em resolução legível (letras grandes o bastante para quem assistir)
- [ ] Narrar o que está acontecendo em cada passo — não deixar silêncio enquanto a tela carrega
- [ ] Ter o template já escrito e testado antes de começar a gravar (evita erro de digitação ao vivo)
- [ ] Cronometrar: essa demo cabe em ~8-10 min, deixando o restante dos 15-20 min da apresentação para os outros tópicos do roteiro
- [ ] Encerrar mostrando a stack e os recursos já removidos, reforçando o ciclo de vida completo (criar → usar → destruir)
