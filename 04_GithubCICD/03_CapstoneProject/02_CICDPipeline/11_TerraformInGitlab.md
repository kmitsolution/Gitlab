To implement a **Terraform script to provision AWS infrastructure for GitLab CI/CD**, the typical use case is to **automate creation of AWS resources** (e.g., VPC, EC2, EKS, S3, IAM roles) for your GitLab deployment or CI jobs.

Below is a **simple and real-world example** of a Terraform script that:

✅ Creates an S3 bucket  
✅ Creates an IAM user for GitLab with limited access  
✅ Outputs credentials to be used as GitLab CI/CD variables

---

## 📁 Terraform Structure

```
aws-gitlab/
├── main.tf
├── variables.tf
└── outputs.tf
```

---

## 📝 `main.tf`

```hcl
provider "aws" {
  region = var.region
}

resource "aws_s3_bucket" "gitlab_ci_artifacts" {
  bucket = var.bucket_name
  acl    = "private"
}

resource "aws_iam_user" "gitlab_ci_user" {
  name = "gitlab-ci-user"
}

resource "aws_iam_access_key" "gitlab_ci_key" {
  user = aws_iam_user.gitlab_ci_user.name
}

resource "aws_iam_user_policy" "gitlab_ci_policy" {
  name = "GitLabCIUserPolicy"
  user = aws_iam_user.gitlab_ci_user.name

  policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Effect = "Allow"
        Action = [
          "s3:*"
        ]
        Resource = [
          aws_s3_bucket.gitlab_ci_artifacts.arn,
          "${aws_s3_bucket.gitlab_ci_artifacts.arn}/*"
        ]
      }
    ]
  })
}
```

---

## 🔧 `variables.tf`

```hcl
variable "region" {
  default = "us-east-1"
}

variable "bucket_name" {
  default = "gitlab-ci-artifacts-12345"
}
```

---

## 📤 `outputs.tf`

```hcl
output "aws_access_key_id" {
  value = aws_iam_access_key.gitlab_ci_key.id
  sensitive = true
}

output "aws_secret_access_key" {
  value = aws_iam_access_key.gitlab_ci_key.secret
  sensitive = true
}

output "s3_bucket_name" {
  value = aws_s3_bucket.gitlab_ci_artifacts.bucket
}
```

---

## 🚀 Usage Steps

1. **Initialize and Apply**

```bash
terraform init
terraform apply
```

2. **Set GitLab CI/CD Variables**

Go to **GitLab → Project → Settings → CI/CD → Variables**, and add:

| Key                     | Value from Terraform |
|--------------------------|-----------------------|
| `AWS_ACCESS_KEY_ID`     | `aws_access_key_id` output |
| `AWS_SECRET_ACCESS_KEY` | `aws_secret_access_key` output |
| `S3_BUCKET`             | `s3_bucket_name` output |

---

## ✅ Now You Can:

- Upload CI/CD artifacts to the S3 bucket
- Use AWS CLI in your `.gitlab-ci.yml` for deployment, backups, logs, etc.

---

