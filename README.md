
# Implementar uma Infraestrutura Automatizada com AWS CloudFormation

O AWS CloudFormation é um serviço da AWS que permite criar, atualizar e gerenciar toda a infraestrutura da nuvem de forma automatizada e reprodutível, usando código (YAML ou JSON).

Em vez de criar manualmente instâncias EC2, grupos de segurança, VPCs, etc., você descreve todos esses recursos em um template, e o CloudFormation se encarrega de criar tudo automaticamente.

---

## Etapas da Implementação

### Criar o Template
Um **template CloudFormation** é um arquivo `.yaml` que descreve os recursos desejados.

Exemplo simples: criar uma instância EC2 com servidor Apache e grupo de segurança.

```yaml
AWSTemplateFormatVersion: 2010-09-09
Description: Infraestrutura automatizada com AWS CloudFormation — Servidor Web Apache
Resources:
  WebServerInstance:
    Type: AWS::EC2::Instance
    Properties:
      AvailabilityZone: us-east-1a
      ImageId: ami-0ed9277fb7eb570c9
      InstanceType: t2.micro
      Tags:
        - Key: "Name"
          Value: "ServidorWeb"
      SecurityGroups:
        - !Ref WebSecurityGroup
      UserData:
        Fn::Base64: !Sub |
          #!/bin/bash -xe
          yum install -y httpd.x86_64
          systemctl start httpd.service
          systemctl enable httpd.service
          echo "<h1>Infraestrutura criada com AWS CloudFormation — $(hostname -f)</h1>" > /var/www/html/index.html

  WebSecurityGroup:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription: Permitir acesso HTTP e SSH
      SecurityGroupIngress:
        - IpProtocol: tcp
          FromPort: 80
          ToPort: 80
          CidrIp: 0.0.0.0/0
        - IpProtocol: tcp
          FromPort: 22
          ToPort: 22
          CidrIp: 0.0.0.0/0
```

### Criar a Stack

A Stack é o conjunto de recursos criados a partir do template.

#### Via Console
- Acesse o serviço **CloudFormation** no console AWS.
- Clique em **Create Stack → With new resources (standard)**.
- Envie o arquivo `.yaml`.
- Escolha um nome, por exemplo: `StackWebServer`.
- Clique em **Next → Next → Create Stack**.

### Acompanhar a Criação

O CloudFormation exibirá o status:
```
CREATE_IN_PROGRESS → CREATE_COMPLETE
```
Quando finalizar, sua infraestrutura estará pronta:
- EC2 ativa
- Grupo de segurança configurado
- Servidor Apache funcionando

---

### Gerenciar e Atualizar

Para mudar algo (ex: tipo da instância), basta editar o template e fazer upload novamente.

---

## Benefícios da Automação

✅ Reprodutibilidade  
✅ Controle de versão (GitHub)  
✅ Padronização  
✅ Gerenciamento centralizado  
