This project takes an existing monolithic application and 
divides it into microservices. The original application
allow users to post new images with captions to 
the growing feed. This capability sat behind authentication,
and new users were able to register.

The application makes use of AWS's RDB, configured as a Postgres
database, and AWS's S3 for file storage. So when a new
image is posted, a new database entry is made containing
a reference to the file which is PUT on S3. The user-related
transactions which allow new users to register and returning
users to authenticate depend on the same Postgres database 
but do not access the S3 bucket.

The application began with a frontend and a backend, but
the backend was a single monolithic server. It has now been refactored
into 2 separate services, one for feed-related activity
and one for user-related activity. The frontend remained
intact with some configuration changes.

A reverse proxy was set up to direct traffic from the frontend
to either of the two backend services. It is configured to discern which
should go where by looking at the names of the api calls.

The frontend, backend feed, backend user and reverseproxy pieces
are each in their own Docker containers.

To allow for continuous integration, this github repository
was integrated with travis-ci. Whenever a change is pushed
to main, the process defined in docker-compose-build.yml is
kicked off with the code currently checked into github.

Once updates to the Docker hub are complete, Kubernetes 
can pull the latest versions of the containers into a
Kubernetes namespace where Kubernetes deploy and service
files define the configuration of each pod and service
as a whole. 

The frontend and reverseproxy services need to be publicly
exposed, that is, available to users that are not within
the Kubernetes cluster. Traffic to this accessible front-end is 
directed to the (also externally accessible) reverseproxy which in turn directs it to either the feed service or the user service -- which are
both only available internally within the cluster.

AWS credentials for access to S3 was perhaps the most
difficult to understand and configure. Ultimately, the
AWS credentials from ~/.aws/credentials file were base64-encoded,
*including* the name of the AWS_PROFILE. The lines:

```bash
[default]
aws_access_key_id=<access key>
aws_secret_access_key=<secret access key>
```

were encoded and stored in a file called aws-secret, as "credentials."
The contents of this credentials field then appears as a mounted
file within the backend-feed pod, at the specified mount location.
Once this is in place, the feed api uses AWS.SharedIniFileCredentials
to generate an credentials object, which enables creation of
signed urls - so that a non-AWS user - a public user - may view
files from S3 and create them, through the application.

Finally, horizontal scaling was set up on the
backend feed service so that an appropriate number of 
pod replicas is available, depending on the monitored cpu-usage.


