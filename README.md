# devops-netologynew line
# Local .terraform directories
**/.terraform/* - будут проигнорированы все каталоги внутри данного

# .tfstate files
*.tfstate - будут проигнорированы файлы с расширением .tfstate
*.tfstate.* - будут проигнорированы файлы .tfstate.

# Crash log files
crash.log - будут проигнорированы файлы crash.log

# Exclude all .tfvars files, which are likely to contain sentitive data, such as
# password, private keys, and other secrets. These should not be part of version
# control as they are data points which are potentially sensitive and subject
# to change depending on the environment.
#
*.tfvars - будут проигнорированы файлы с расширением .tfvars

# Ignore override files as they are usually used to override resources locally and so
# are not checked in
override.tf - будут проигнорированы файлы override.tf
override.tf.json - будут проигнорированы файлы override.tf.json
*_override.tf - будут проигнорированы файлы _override.tf
*_override.tf.json - будут проигнорированы файлы _override.tf.json

# Include override files you do wish to add to version control using negated pattern
#
# !example_override.tf

# Include tfplan files to ignore the plan output of command: terraform plan -out=tfplan
# example: *tfplan*

# Ignore CLI configuration files
.terraformrc - будут проигнорированы файлы с расширением .terraformrc
terraform.rc - будут проигнорированы файлы с расширением terraformrc.rc
