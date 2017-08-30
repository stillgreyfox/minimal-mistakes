# Setup guide for S3-hosted, Cloudflare cached, static Jekyll website

Based on learnings from here:
[Mind the Cloud](http://blog.mindthecloud.com/2014/08/31/create-your-static-blog-from-scratch-in-1-hour.html)

    # clone this repo
    git clone git@github.com:stillgreyfox/stillgreyfox.github.io.git
    cd stillgreyfox.github.io


## Jekyll and Tools
    
    sudo gem install bundler jekyll
    git clone git@github.com:s3tools/s3cmd.git
    s3cmd --configure
    bundle install --binstubs --path=vendor/bundle


## Cloudflare

1. Have Cloudflare copy existing domain DNS settings
2. Delete records on registrar's DNS
3. Point domain registrar to Cloudflare DNS servers


## S3 Buckets

    # Make bucket <yourdomain.com>
    s3cmd mb s3://<YOURDOMAIN>_log

    # Make bucket <yourdomain_logs>
    s3cmd mb s3://<YOURDOMAIN.COM>

    # Setup logging
    s3cmd accesslog --access-logging-target-prefix=s3://<YOURDOMAIN>_log s3://<YOURDOMAIN.COM>

    # Set 404 page (use AWS console)


## IAM Security
Setup an access credential to S3, full control for use with s3cmd to deploy

	{
		"Version": "2008-10-17",
		"Statement": [
			{
				"Sid": "AddPerm",
				"Effect": "Allow",
				"Principal": "*",
				"Action": "s3:GetObject",
				"Resource": "arn:aws:s3:::<YOURDOMAIN.COM>/*"
			}
		]
	}


## Deployment

    # check your 'url' and 'baseurl' settings
    vi _config.yml

    # clean and rebuild
    rm -rf _site
    bundle exec jekyll build

    # push to S3
    s3cmd sync _site/ s3://<YOURDOMAIN.COM>
    s3cmd sync _site/ s3://narlson.com
