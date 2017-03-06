# infrastructureascode/route53-presence

Docker container which registers the local EC2 instance into Route53.

This is a fork of [timelinelabs/docker-route53-presence](https://github.com/timelinelabs/docker-route53-presence) which doesn't have a Docker Hub repo.  I have updated the script so that it can handle hostnames with 3rd-level domains (i.e. host.subdomain.example.com).  I have sent a [pull request](https://github.com/timelinelabs/docker-route53-presence/pull/1) to get the change incorporated, but decided to roll on my own given that repo hasn't been updated since March 2014.

```
docker run --rm infrastructureascode/route53-presence -h
usage: route53-presence [-h] [--ttl TTL] [--local] hostname

Register or unregister a name in Route53.

positional arguments:
  hostname    fqdn to manipulate

optional arguments:
  -h, --help  show this help message and exit
  --ttl TTL   ttl in seconds, default 600
  --local     use local IP instead of public
```

## Example Usage

```
# Passing AWS keys as environment variables
docker run --name route53-presence  \
    -e AWS_ACCESS_KEY=XXX \
    -e AWS_SECRET_KEY=XXX \
    infrastructureascode/route53-presence \
        --ttl=300 \
        host.subdomain.example.com

# Omitting AWS keys if the EC2 instance has a role that allows Route 53 actions. See below.
docker run --name route53-presence  \
    infrastructureascode/route53-presence \
        --ttl=300 \
        host.subdomain.example.com
```

## AWS IAM Role

The AWS credentials can be omitted if the host has a valid IAM role that allows for the following permissions:

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "route53:GetHostedZone",
                "route53:ChangeResourceRecordSets"
            ],
            "Resource": "arn:aws:route53:::hostedzone/<ZONEID>"
        },
        {
            "Effect": "Allow",
            "Action": [
                "route53:ListHostedZones"
            ],
            "Resource": "*"
        }
    ]
}
```
