
# Linux Server Config
---------------------------

## Details
1. IP Address = 
2. ssh port = 
3. URL = 

## Summary of software and configuration changes made

### Update package lists
1. sudo apt-get update
2. sudo apt-get upgrade

### Create new user account 'grader' and give usdo access
1. sudo adduser grader
2. sudo touch /etc/sudoers.d/grader
3. sudo nano /etc/sudoers.d/grader 
added 'grader ALL=(ALL:ALL) ALL' to the file before saving and exiting

### Create SSH keypair
1. ssh-keygen -t rsa (on my local machine and saved the key to the file /Users/kwakuakoi/.ssh/udacity_key)
2. su - grader (to log in as grader)
3. mkdir .ssh
4. touch .ssh/authorized_keys
5. Copied the public key from /Users/kwakuakoi/.ssh/udacity_key.pub and saved it to the remote server
6. sudo chmod 700 .ssh
7. sudo chmod 644 .ssh/authprized_keys
8. exit (logout grader)
9. ssh grader@18.221.245.98 -p 22 -i ~/.ssh/udacity_key (currently getting a Permission denied (publickey))
