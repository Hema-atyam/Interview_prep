
# Terraform Interview Prep - Real-World Scenarios & Q&A

## ✅ BASICS

### Q1. What is Terraform and how does it work?
- IaC tool by HashiCorp using HCL.
- Works via: `terraform init` → `plan` → `apply`.

### Q2. What is a provider in Terraform?
- Plugin to interact with cloud services (e.g., AWS, Azure).

### Q3. What is the Terraform state file?
- Tracks the actual state of infrastructure.
- Use `terraform.tfstate`, can be stored in S3 for team use.

### Q4. What are variables in Terraform?
- Parameterize infra using `variable` blocks and `tfvars` files.

### Q5. How do you manage multiple environments?
- Use workspaces or separate folders (`dev/`, `prod/`) with different backend `key`s.

---

## 🔁 INTERMEDIATE / PRACTICAL USAGE

### Q6. How do you manage Terraform state in a team?
- Store in **S3 backend**, enable **DynamoDB locking**, enable **versioning**.

### Q7. What are modules?
- Reusable blocks for infra (e.g., VPC, EC2).
- Helps organize large projects.

### Q8. How to handle secrets in Terraform?
- Use environment vars, AWS SSM, or Secrets Manager.
- Avoid hardcoding.

### Q9. A developer made changes manually in AWS. What do you do?
- Use `terraform import` to bring resource into state.

### Q10. count vs for_each vs dynamic block?
- `count`: identical resources
- `for_each`: from a map/set
- `dynamic`: generate nested blocks dynamically

### Q11. How to prevent downtime when Terraform recreates a resource?
- Use `create_before_destroy` in `lifecycle` block.

### Q12. How to handle dependencies between resources?
- Use references (`resource.id`), or explicit `depends_on`.

---

## 🛠️ REAL-WORLD SCENARIOS / CHALLENGES

### Q13. Module was deleted, but state still has the resource. What now?
- Use `terraform state rm` to clean it up.

### Q14. Project is growing. How do you structure Terraform code?
- Use a **modular layout** with `modules/` and `environments/`.

### Q15. What is a data source and when is it used?
- Used to **fetch existing resources** like latest AMIs, existing VPCs, etc.

### Q16. Hands-on Challenge: Create EC2 instance with:
- Latest Ubuntu AMI (using data source)
- SSH key from SSM Parameter Store
- Output public IP

### Q17. Terraform apply failed mid-way. Resources half-created. What now?
- Inspect state, use `import` or `taint` to fix, re-apply.

### Q18. State file in S3 got corrupted and no backup. How to recover?
- Restore via S3 **versioning** (if enabled)
- If not, **re-import manually** using resource IDs

### Q19. How to manage code for multi-tenant or multi-env infra?
- Use folder structure (`env/dev`, `env/prod`) with separate backend state.

### Q20. How do you auto-trigger Terraform apply on Git push?
- Use Jenkins, GitHub Actions, GitLab CI, etc. with secure credentials and pipeline steps.

---

## 🔄 TROUBLESHOOTING & RECOVERY CHEATSHEET

| Issue                      | Solution                                |
|---------------------------|-----------------------------------------|
| Corrupted state file      | Restore from S3 version (if enabled)    |
| No backup                 | Use `terraform import` for each resource|
| Partial resources created | Use `taint` or `import` and reapply     |
| Module deleted            | Use `terraform state rm`                |
| Secrets hardcoded         | Move to AWS SSM/Secrets Manager         |
| Unwanted destroy on apply | Use `lifecycle { prevent_destroy = true }` |
