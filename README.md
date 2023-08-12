# Wordpress on docker with s3 backend

:warning: **This project is an example, do not set insecure password in your docker compose !** :warning:

1. Create tree structure

```bash
mkdir -p mariadb/data minio/data wordpress/data wordpress/php wordpress/plugins
```

Downaload and unzip [media cloud plugin](https://github.com/Interfacelab/ilab-media-tools/releases) into wordpress/plugins

:warning: **Don't use this policy in production !** :warning:

```bash
touch minio/wordpress-s3.json
cat << EOL > minio/wordpress-s3.json 
{
  "Version": "2012-10-17",
  "Statement": [
      {
          "Sid": "wodpress",
          "Effect": "Allow",
          "Action": [
              "s3:*"
          ],
          "Resource": [
              "arn:aws:s3:::wordpress"
          ]
      }
  ]
}
EOL
```

```bash
touch wordpress/php/wordpress.ini
cat << EOL > wordpress/php/wordpress.ini
file_uploads = On
memory_limit = 256M
upload_max_filesize = 100M
post_max_size = 100M
max_execution_time = 120
max_input_time = 1000
EOL
```

2. After deploying docker-compose and configuring wordpress, you need to change your local right for wordpress (issue with image/plugins... upload) :

```bash
sudo chmod www-data:www-data ./wordpress/data
```

3. Configure wordpress **media cloud** plugin

for manual install [plugin](https://github.com/Interfacelab/ilab-media-tools/releases/download/4.5.21/ilab-media-tools.4.5.21.zip)

- [x] Check Cloud Storage in the general plugin setup and save change
go to the plugins setting,
- [x] Check "Enable Cloud Storage"
- [x] Select minio as Storage provider
- [x] Setup "Provider Settings" (check "Path Style Endpoint")
- [x] Change "Upload Privacy ACL" to private
- [x] Check "Detele From Storage"
- [x] Check "Use Pre-Signed URLs"
- [x] Reduce "Pre-Signed URL Expiration" to 1 min

Save each section individually

4. Test your plugin setting

Go to "System Test" plugin
and run a test

## Further informations

If you want to use existing bucket images/documents, you need to import your s3 data into wordpress/data/wp-content/uploads.
Then synchronous with media cloud plugin
