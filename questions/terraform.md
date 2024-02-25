## Terraform



<details>
  <summary>Что содержит код Terraform?</summary>

Ресурсы облачного провайдера, а также провижининг для создаваемых ресурсов.

</details>


<details>
  <summary>Как хранить состояние инфраструктуры в Terraform?</summary>

Например, можно хранить tfstate в git-репозитории команды. Другой вариант - хранить в специализированном Terraform Backend.

</details>



<details>
  <summary>Terraform Backend. Какой лучше?</summary>

Зависит от требованиям к хранению состояния.

- AWS S3 — Standard (с блокировкой через DynamoDB). Сохраняет состояние в виде заданного ключа в заданном сегменте на Amazon S3. Этот бэкэнд также поддерживает блокировку состояния и проверку согласованности через DynamoDB.

- terraform enterprise — Standard (без блокировки).

- etcd — Standard (без блокировки). Сохраняет состояние в etcd 2.x по заданному пути.

- etcdv3 — Standard (с блокировкой). Сохраняет состояние в хранилище etcd в виде K/V с заданным префиксом.

- gcs — Standard (с блокировкой). Сохраняет состояние как объект в настраиваемом префиксе в заданном сегменте в Google Cloud Storage (GCS). Этот бэкэнд также поддерживает блокировку состояния.

- Gitlab Terraform state (с блокировкой). Хранит состояние в Gitlab Terraform state хранилище, используя HTTP протокол и права Gitlab для доступа.

Существуют также и другие Backend для Terraform.

</details>



<details>
  <summary>Как добавить имеющиеся ресурсы в tfstate?</summary>

```
terraform import [options] ADDRESS ID
```
1. Например, создаем директорию и инициализируем будущую инфраструктуру:
```
mkdir terraform-test
cd terraform-test
terraform init
vi main.tf
```
2. Добавляем в файл main.tf следующий код:
```
provider "aws" {
  region = "us-west-1"
  profile = "tyx-local"
}
resource "aws_s3_bucket" "sample_bucket" {
  bucket = "tyx-local-bucket"
  acl = "public"
}
```
3. Выполняем импорт ресурса:
```
terraform import aws_s3_bucket.sample_bucket tyx-local-bucket
```

</details>



<details>
  <summary>Зачем нужен `terraform taint`?</summary>

Команда `terraform taint` пометит ресурс инфраструктуры, который будет удален и заново создан при следующем применении команды `terraform apply`. 

</details>



<details>
  <summary>Как проводить тестирование terraform?</summary>

`terraforn plan` выполнит проверку действующего кода. Работу с облачными ресурсами выполнит 

</details>



<details>
  <summary>Что такое модуль в terraform? Для чего он нужен?</summary>

Модуль в Terraform - пакет конфигурации Terraform, который можно использовать при повторной конфигурации компонентов инфраструктуры, а также базовой организации кода Terraform в директориях. При подключения модуля, ему даётся имя.

</details>



<details>
  <summary>Как хранить переменные в terraform?</summary>

*main.tf* - основной конфигурационный файл, описывающий какие инстансы необходимо создать.
*variables.tf* - конфигурация с описанием переменных и значениями по-умолчанию. Если значения по-умолчанию не задано, то они являются обязательными.
*terraform.tfvars* - конфигурация со значениями переменных. Часто является секретным файлом, поэтому нужно с осторожностью пушить в публичные репозитарии.
*outputs.tf* - описание выходных переменных. Необязательный файл, но очень удобно выделять нужные параметры из созданного инстанса, например IP созданного в облаке инстанса.

</details>



<details>
  <summary>Как конвертировать Kubernetes yaml-манифест в HCL средствами Linux и terraform?</summary>

Например:
```
echo 'yamldecode(file("filename.yaml"))' | terraform console
```

</details>



<details>
  <summary>Что такое Workspaces в Terraform?</summary>

[Workspaces](https://developer.hashicorp.com/terraform/language/state/workspaces#using-workspaces) в Terraform - это возможность управления state файлами. Workspace содержит все что необходимо для управления набором инфраструктуры, а отдельные рабочие области функционируют как полностью отдельные рабочие каталоги. С помощью Workspaces возможно управлять несколькими средами инфраструктуры.

</details>



<details>
  <summary>Для чего нужен terragrunt?</summary>

Terragrunt — это обертка для Terraform, позволяющая решать проблемы, связанные с масштабированием и переиспользованием кода для настройки инфраструктуры. Он позволяет повторно использовать конфигурационные параметры и поддерживает многоуровневые конфигурации и зависимости.

</details>



<details>
  <summary>Чем отличается `count` от `for_each`?</summary>

`count` — это итерация по списку, который содержит целочисленные элементы, `for_each` — это итерация по корневым ключам словаря, которые могут содержать данные любого типа.

```
resource "aws_instance" "web" {
  count = 3
 
  instance_type = "t2.micro"
  ami           = data.aws_ami.debian_buster.id
  tags = {
    Name = "WebServer-${count.index + 1}"
  }
}
```
Описание ресурса выше создаст 3 одинаковых EC2 инстанса, изменив имя с указанием номера текущего состояния счётчика. `count` начинает отсчет с 0, поэтому чтобы 1 EC2 инстанс был с индексом 1 в имени ему прибавили `1`.

```
resource "aws_instance" "server" {
  for_each = {
    web = { type = "t2.micro", public_ip = true },
    db  = { type = "m5.large", public_ip = false }
  }
 
  instance_type = each.value["type"]
  ami           = data.aws_ami.debian_buster.id
  associate_public_ip_address = each.value["public_ip"]
  tags = {
    Name = "each.key"
  }
}
```
Ресурс выше создаст 2 EC2 инстанса с итерацией по ключам `each.key` и использовав значения вложенных словарей в конфигурации EC2.

</details>
