# 🧩 Terraform Modules Lab 

In this lab, you learned how to **use Terraform modules from the public registry**, inspect their required arguments, override default behavior, and control which internal resources a module should create.

This includes reading module documentation, understanding module inputs/outputs, and customizing module behavior through arguments like `create_user`, `create_iam_access_key`, etc.

---

## 📋 Lab Overview

**Goal:**

* Work with a Terraform module from the registry
* Inspect required inputs
* Understand why modules may create multiple resources
* Override default behavior to limit what the module creates
* Deploy a single IAM user using only module input variables

**Learning Outcomes:**

* How to identify required arguments of a module
* How module defaults influence what resources get created
* How to disable optional behavior that modules enable by default
* How to read execution plans to see what Terraform will create

---

## 🛠 Step-by-Step Journey

### Step 1: Navigate to the Project Sapphire Directory

Directory:

```
/root/terraform-projects/project-sapphire
```

Inside `main.tf`, you see:

✔️ A **module block** is already defined.

---

### Step 2: Identify the Module Source

The module references the Terraform Registry:

➡️ **`terraform-aws-modules/iam/aws//modules/iam-user`**

This confirms it is using a **public registry module**.

---

### Step 3: Identify the Module Version

From `main.tf`, the version is set to:

➡️ **Version: `X.X.X` (from your lab it appears fixed by the starter file)**

---

### Step 4: How Many Required Arguments?

Checking the registry documentation:

➡️ **The module requires only ONE argument:**
**`name`**

This is the only mandatory input for creating an IAM user.

---

### Step 5: What Argument is Required to Create an IAM User?

Correct answer:

➡️ **`name`**

This creates the IAM user when using the module.

---

### Step 6: Update the Module to Create User “Max”

Inside `main.tf`, update:

```hcl
module "iam_iam-user" {
  source  = "terraform-aws-modules/iam/aws//modules/iam-user"
  version = "X.X.X"

  name = "max"
}
```

Run:

```bash
terraform init
terraform plan
```

✔️ Successful
✔️ Plan shows resources to be created

---

### Step 7: How Many Resources Will Terraform Create?

Terraform plan shows:

➡️ **3 resources**

---

### Step 8: Which Resources Are Created?

The module creates the following by default:

1. **aws_iam_user**
2. **aws_iam_user_login_profile**
3. **aws_iam_access_key**

These additional resources are generated automatically because the module has defaults that create them unless explicitly disabled.

---

### Step 9: Why Are Extra Resources Created?

From documentation:

➡️ The module includes optional functionality (access key, login profile)
➡️ These optional features are enabled by default
➡️ So even if you only supply `name`, the module still creates all defaults

---

### Step 10: Modify the Module to Only Create the IAM User

Disable the two optional resources:

```hcl
module "iam_iam-user" {
  source  = "terraform-aws-modules/iam/aws//modules/iam-user"
  version = "X.X.X"

  name = "max"

  create_user                      = true
  create_iam_access_key            = false
  create_iam_user_login_profile    = false
}
```

Run:

```bash
terraform plan
```

The plan now shows:

➡️ **Only 1 resource will be created** (the IAM user)

---

## ✅ Key Commands Summary

| Purpose                | Command                           |
| ---------------------- | --------------------------------- |
| Initialize module      | `terraform init`                  |
| View planned resources | `terraform plan`                  |
| Apply module resources | `terraform apply`                 |
| Document module inputs | Terraform Registry → module docs  |
| Limit module behavior  | Set optional arguments to `false` |

---

## 💡 Notes / Tips

* Modules often bundle **multiple resources** — always check defaults.
* Use Terraform Registry docs to understand optional vs required inputs.
* If you only want a subset of resources, disable others explicitly.
* Modules help enforce consistency and reduce repeated code.

---

## 📌 Lab Summary

| Step                    | Status | Key Takeaway                                |
| ----------------------- | ------ | ------------------------------------------- |
| Identify module         | ✅      | Module block defines external module usage  |
| Inspect source          | ✅      | Uses Terraform Registry                     |
| Required argument       | ✅      | Only `name` is mandatory                    |
| Create IAM user         | ✅      | `name = "max"`                              |
| Extra resources created | ✅      | Module defaults create keys + login profile |
| Disable extras          | ✅      | Set two flags to `false`                    |
| Final plan              | ✅      | Only one resource created                   |
