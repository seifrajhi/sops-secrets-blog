## Via Terraform
You will need a S3 bucket for tfstate and a DynamoDB table for tfstate lock.

If you don't have those:
```bash
aws s3api create-bucket --create-bucket-configuration LocationConstraint="your_region" --bucket your_bucket_name

aws dynamodb create-table --attribute-definitions AttributeName=LockID,AttributeType=S --key-schema AttributeName=LockID,KeyType=HASH --provisioned-throughput ReadCapacityUnits=1,WriteCapacityUnits=1 --table-name your_table_name
```

Now you can use the above created for the next steps.

You will need to set the following values, in `locals.tf`:
```yaml
locals {
    admin_user  = "your_admin_user"
    sops_user   = "sops-user"
}
```

Then in `terraform-backend.tf`:
```yaml
terraform {
  backend "s3" {
    bucket         = "your_bucket"
    key            = "your_bucket_tfstate_file"
    region         = "your_region"
    dynamodb_table = "your_dynamodb_table"
  }
}
```

To deploy:
```bash
terraform init
terraform apply
```

