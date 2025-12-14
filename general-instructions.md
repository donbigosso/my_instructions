# Command Center Dev and Prep Instructions #
## Preparation ##
### PHP and MySQL containers ###

File and folder structure: 
```
your-project/ 
│ 
├── docker-compose.yml 
├── src/ 
│   └── index.php 
└── db/ 
    └── dump.sql      # your SQL file here (any name ending with .sql) 
```

docker-compose.yml content:

```
services:
  php:
    build: .
    container_name: php-server
    ports:
      - "8082:80"  # Maps port 8082 on your computer to port 80 in the container. 8082 can be replaced by any other port.
    volumes:
      - ./src:/var/www/html
    depends_on:
      - mysql
    restart: unless-stopped

  mysql:
    image: mysql:8.0
    container_name: mysql-db
    environment:
    #change the below credentials to your own use
      MYSQL_ROOT_PASSWORD: rootpass
      MYSQL_DATABASE: appdb
      MYSQL_USER: appuser
      MYSQL_PASSWORD: userpass
    ports: 
      - "3306:3306" # this is needed to connect from outside of the container (for example using Sequel Ace)
    volumes:
      - db_data:/var/lib/mysql
      - ./db:/docker-entrypoint-initdb.d
    restart: unless-stopped

volumes:
  db_data:
```
Ports can be adjusted according to the need. <br>
Last lines declare the named volume (creates a persistent storage named db_data).
<br>
To check the volume use:
```
docker volume ls

docker volume inspect db_data
```
 To clear it (needed for example if database has been changed) use:
```
docker compose down -v
```
Dockerfile content:

```
FROM php:8.2-apache

# Install PDO MySQL extension
RUN docker-php-ext-install pdo pdo_mysql
```

## Testing connection and functionalities ##

index.php file content:
```
<?php
$pdo = new PDO('mysql:host=mysql;dbname=appdb', 'appuser', 'userpass');
echo "Connected!<br>";

$stmt = $pdo->query("SHOW TABLES");
while ($row = $stmt->fetch()) {
    echo $row[0] . "<br>";
}

?>
```
Script for file permissions (uploads folder must be created first)

```
$folder = __DIR__ . '/uploads';
$file = $folder . '/test_file.txt';

// Try to create file
if (file_put_contents($file, "Hello from PHP!\n")) {
    echo "File created successfully at: $file";
// Change file permission to group write
    chmod($file, 0664);  // owner rw, group rw, others r
} else {
    echo "Failed to create file. Check folder permissions!";
}

// Check if file exists
if (file_exists($file)) {
    echo "<br>File contents:<br>";
    echo nl2br(file_get_contents($file)); //new line to <br> - replaces new lines with br to nicely show file contents
}
```
When this script returns error, make sure folder is either set to 777 or group ownership is changed to www-data and permissions are set as 775:
```
chown -R bigos:www-data uploads/  #bigos is owner www-data is group

chmod 775 uploads/ #group can write and execute


```
Using DB CLI
```
docker exec -it mysql-db mysql -u myuser -p mydatabase #change to your user and your db name; mysql-db is a container name
```
or
```
docker compose exec mysql mysql -u root -p #when using root password
```


## Git ##
Checking if git is installed
``` git --version ``` <br>
Initiating local git repository
``` git init ```
To ignore files from committing add them (create if not present) to ``` .gitignore ``` file, for example:
```
*.html
test_file.md
 ```
 Adding files for committing ``` git add . ``` <br>
 
 Committing files: 
 ``` git commit -m "Commit message here" ```

Possible error:


```
Author identity unknown

*** Please tell me who you are.

Run

  git config --global user.email "you@example.com"
  git config --global user.name "Your Name"

to set your account's default identity.
Omit --global to set the identity only in this repository.

fatal: unable to auto-detect email address (got 'bigos@raspberrypi.(none)')
```

### Remote origin ###


Checking if there is a remote origin ``` git remote -v ``` . It will return nothing if origin is not set up.
### Setting up git origin ###
1. Create new repository on GitHub (no .gitignore, no README). After creating it instructions will be shown in GitHub.
2. Run the below commands in the terminal.
<br>If the branch is not called main (old repos): ``` git branch -M main ```
Then: ``` git remote add origin git@github.com:username/repository_name.git ``` You can find it in code tab under SSH on GitHub.
<br> Finally, push the content: ``` git push -u origin main ```

### Cloning an existing repository ###
``` git clone https://github.com/username/repo_name ```

### Fetch vs pull ###

``` git fetch ``` → Downloads new changes from the remote repository to your local .git folder, but doesn't change your working files.

``` git pull ``` = ``` git fetch ``` +  ``` git merge ``` → Downloads and immediately merges the changes into your current branch.

Pulling from origin
``` git pull origin main ```
