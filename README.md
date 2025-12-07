Terraform

985  gh repo create terraform-iac --public --source=. --remote=origin --push\n
  986  git remote -v
  987  git remote remove origin\n
  988  git remote -v\n
  989  git remote add origin https://github.com/sherinbinny/terraform-iac.git
  990  git remote -v\n
  991  git push -u origin main\n
  992  git pull
  993  brew tap hashicorp/tap
  994  brew install hashicorp/tap/terraform
