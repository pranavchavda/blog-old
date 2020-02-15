# blog
Personal Blog

# to upload
hugo && s3cmd --no-mime-magic --acl-public --delete-removed --delete-after sync public/ s3://blog
