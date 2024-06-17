# Passo a Passo para Criar uma Instância EC2 com Terraform no Windows.
## Ponderada M10 S9

Nesse tutorial, você aprenderá a criar uma instância EC2 na AWS de forma automatizada utilizando o Terraform, uma ferramenta IaC que possibilita a criação e gestão de recursos em diversos serviços de nuvem. Siga os passos abaixo para subir a sua primeira instância EC2 na AWS usando o Terraform! 

### 1. Primeiro, acesse a sua conta no [AWS Academy](https://awsacademy.instructure.com/) e inicie um laboratório da AWS. Esse passo é um pré-requisito para que o tutorial possa ser realizado!

## 2. Instalando o Terraform
### 2.1 Abra o Poweshell como administrador;
### 2.2 Volte para a pasta root usando o comando ```cd ..``` até chegar na pasta.
### 2.3 Baixe o Terraforme usando o comando 
```
Invoke-WebRequest -Uri https://releases.hashicorp.com/terraform/1.0.11/terraform_1.0.11_windows_amd64.zip -OutFile terraform.zip
```
### 2.4 Após baixar, extraia o arquivo usando o comando 
```
Expand-Archive -Path terraform.zip -DestinationPath C:\terraform
```
### 2.5 Após extraído, adicione o Terraform ao PATH do seu sistema usando o comando 
```
[System.Environment]::SetEnvironmentVariable('PATH', $env:PATH + ';C:\terraform', [System.EnvironmentVariableTarget]::Machine)
```

![1](https://res.cloudinary.com/dwxvglcja/image/upload/v1718590246/1_fvpotr.png)

### 2.6 Feche o Poweshell e abra novamente. Volte para a pasta root e execute o comando ```terraform -v``` para verificar se o Terraform foi instalado corretamente. 

![2](https://res.cloudinary.com/dwxvglcja/image/upload/v1718590246/2_fl9yzg.png)


## 3. Instalando o AWS CLI
### 3.1 Abra o Poweshell como administrador;
### 3.2 Volte para a pasta root usando o comando ```cd ..``` até chegar na pasta.
### 3.3 Baixe o instalador do AWS CLI usando o comando 
```
Invoke-WebRequest -Uri https://awscli.amazonaws.com/AWSCLIV2.msi -OutFile AWSCLIV2.msi
```
### 3.4 Agora, instale o AWS CLI usando o comando 
```
Start-Process msiexec.exe -Wait -ArgumentList '/I AWSCLIV2.msi /quiet'
```

![3](https://res.cloudinary.com/dwxvglcja/image/upload/v1718590246/3_pvtlgf.png)

### 3.5 Feche o Poweshell e abra novamente. Volte para a pasta root e execute o comando ```aws --version``` para verificar se o AWS CLI foi instalado corretamente. 

![4](https://res.cloudinary.com/dwxvglcja/image/upload/v1718590247/4_n2fhgt.png)


## 4. Configurando as credenciais da AWS
### 4.1 No Learner Lab, clique em ```AWS Detais``` para acessar suas credenciais.
![5](https://res.cloudinary.com/dwxvglcja/image/upload/v1718590246/5_ebepaw.png)

### 4.2 Clique em ```Show``` para visualizá-las.
![6](https://res.cloudinary.com/dwxvglcja/image/upload/v1718590246/6_mjs7qm.png)

### 4.3 Agora, volte para o Powershell como administrador, na pasta root, e execute o comando 
```
aws configure
```

### 4.4 Será solicitado que você informe ```aws_access_key_id``` e ```aws_secret_access_key```, volte para o Learner Lab e cole as informarções no terminal conforme forem solicitadas. A última pergunta você deverá deixar em branco, para isso, basta clicar _Enter_.

![7](https://res.cloudinary.com/dwxvglcja/image/upload/v1718590246/7_jkguxv.png)
![8](https://res.cloudinary.com/dwxvglcja/image/upload/v1718590246/8_uqknfq.png)

### 4.5 Agora, ainda no terminal, navegue até o diretório de configuração da AWS usando o comando 
```
cd ~\.aws\
```

### 4.6 Abra o arquivo _Credentials_ em um editor de texto usando o comando 
```
notepad credentials
```

### 4.7 No arquivo, você deverá encontrar já preenchido com as informações que foram informadas anteriormente. Algo como: 
```
[default]
aws_access_key_id = YOUR_ACCESS_KEY_ID
aws_secret_access_key = YOUR_SECRET_ACCESS_KEY
```

### 4.8 Agora, você irá inserir nesse arquivo a sua ```aws_session_token```. Para isso, volte no Learner Lab e copie essa informação, depois cole no arquivo de texto. 

![9](https://res.cloudinary.com/dwxvglcja/image/upload/v1718590246/9_tp2fo9.png)

**Não esqueça de salvar o arquivo!**


## 5. Criando uma instância EC2 usando o Terraform
### 5.1 Crie uma pasta de projeto em um diretório de sua preferência. 
### 5.2 Abra a pasta criada usando o _Visual Studio Code_ ou outro editor de sua preferência.
### 5.3 Crie um arquivo chamado ```main.tf``` onde deve ser inserido o conteúdo abaixo: 
```
provider "aws" {
  region = "us-east-1"
}

resource "aws_instance" "example" {
  ami           = "ami-0c02fb55956c7d316"  # Amazon Linux 2 AMI (HVM), SSD Volume Type
  instance_type = "t2.micro"

  tags = {
    Name = "TerraformExample"
  }
}
```
![10](https://res.cloudinary.com/dwxvglcja/image/upload/v1718590246/10_u3gwal.png)

**Você pode dar outro nome para a sua instância!**

### 5.4 Agora, abra o Powershell como administrador e navegue até a pasta do seu projeto. 

### 5.5 Dentro da pasta onde está o arquivo _main.tf_ execute o comando ```terraform init``` para inicializar o Terraform.

### 5.6 Agora vamos planejar a infraestrutura. Para isso, execute o comando ```terraform plan```.

![11](https://res.cloudinary.com/dwxvglcja/image/upload/v1718590246/11_rwcel9.png)

### 5.7 Por fim, vamos aplicar a configuração para criar a instância EC2. Para isso, execute o comando 
```
terraform apply
```

Digite ```yes``` quando for solicitado para confirmar a aplicação da configuração. 

![12](https://res.cloudinary.com/dwxvglcja/image/upload/v1718590246/12_esmqfx.png)

OBS: Se você encontrar o erro InvalidAMIID.NotFound, significa que o ID da AMI não é válido para a região especificada. Para encontrar uma AMI válida, você pode usar o comando AWS CLI: 
```
aws ec2 describe-images --owners amazon --filters "Name=name,Values=amzn2-ami-hvm-2.0.????????-x86_64-gp2"
```

Isso retornará uma lista de AMIs válidas que você pode usar. Substitua o ID da AMI em seu arquivo main.tf por uma das AMIs listadas.

### 5.8 Pronto! Sua instância foi criada, para visualizá-la vá no Learner Lab e navegue até suas instâncias EC2. 

![13](https://res.cloudinary.com/dwxvglcja/image/upload/v1718590247/13_yhtbb6.png)


## 6. Destruindo a instância
### 6.1 Caso queira, para destruir sua instância EC2, basta utilizar o comando 
```
terraform destroy
```
Digite ```yes``` quando solicitado para confirmar a destruição da infraestrutura.


![14](https://res.cloudinary.com/dwxvglcja/image/upload/v1718592422/14_f2fcdw.png)


## 7. Referências:
1. Afonsinho. Cloud. Disponível em: https://aulas-ttox.onrender.com/mod10es/cloud. Acesso em: 16 jun. 2024.
2. HASHICORP DEVELOPER. Build infrastructure with Terraform. Disponível em: https://developer.hashicorp.com/terraform/tutorials/aws-get-started/aws-build. Acesso em: 16 jun. 2024.

