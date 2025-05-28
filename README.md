# Desafio-3
Configurar y utilizar roles de AWS IAM desde la línea de comandos (CLI) para permitir la escritura en un bucket de S3.

# Configuración de AWS IAM para escritura en S3 desde CLI

## Objetivo  
Aprender a configurar y utilizar roles de AWS IAM desde la línea de comandos (CLI) para permitir la escritura en un bucket de S3.

## Escenario  
Eres un administrador de sistemas en una empresa que utiliza AWS para sus servicios en la nube. Te han asignado la tarea de configurar un rol de IAM que permita a los usuarios asumirlo desde la CLI para escribir archivos en un bucket de S3 específico.

## Comandos útilizados

### Creación del bucket en S3  
```sh
aws s3 mb s3://bucket-desafio-3
```
### Asignación de una política que permita escribir en el bucket de S3, cree el json para la asignación
```sh
nano s3_write_policy.json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:PutObject"
      ],
      "Resource": "arn:aws:s3:::bucket-desafio-3/*"
    }
  ]
}
```
### Crear un usuario
```sh
awslocal iam create-user --user-name s3-support
```
### Creo el json para el usuario para obtener permisos de escritura en el bucket
```sh
nano trust_policy.json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::223318880197:user/s3-support"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}

```
### Creo el rol 
```sh
awslocal iam create-role --role-name Role_supportS3 --assume-role-policy-document file://trust_policy.json
```
### Asumir el Role desde el usuario
```sh
awslocal sts assume-role --role-arn arn:aws:iam::223318880197:role/Role_supportS3 --role-session-name TestSession
```
### Realizo prueba de subida de archivos a bucket 
```sh
echo "Este archivo fue creado para verificar funcionalidad de bucket creado para desafio 3" > archivo-prueba.txt
awslocal s3 cp archivo-prueba.txt s3://bucket-desafio-3/archivo-prueba.txt

```
### Revisión de bucket para comprobar archivo subido
```sh
aws s3 ls s3://bucket-desafio-3/
```
