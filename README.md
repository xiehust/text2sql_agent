## Env Setup
### install conda
```bash
mkdir -p ~/miniconda3
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh -O ~/miniconda3/miniconda.sh
bash ~/miniconda3/miniconda.sh -b -u -p ~/miniconda3
rm -rf ~/miniconda3/miniconda.sh
source ~/miniconda3/bin/activate

conda create -n "t2s" python=3.10 -y
conda activate t2s
```
### Config Permission

- IAM Role's permission

Create a new IAM role with name genbirag-service-role and settings below:
   - Trusted entity type: AWS Service
   - Service: EC2
   - Use Case: EC2 - Allows EC2 instances to call AWS services on your behalf.

Skip "Add permission" and create this role first.

After the role is created, and then add permission by creating inline policy as below:
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": [
                "bedrock:*",
            ],
            "Resource": "*"
        }
    ]
}
```

Finally, Bind this IAM instance profile (IAM Role) to your EC2 instance.

- Amazon Bedrock's Model Permission

Make sure you have enabled model access in AWS Console in us-east-1 region for Anthropic Claude 3.5 sonnet model .

### Setup MYSQL
- Install Docker
Log in to the EC2 instance using SSH command as the ec2-user user or use the AWS EC2 Instance Connect feature in the EC2 console to log in to the command line. 

In the session, execute the following commands.
 **Note: Execute each command one line at a time.**
```bash  
# Install components
sudo yum install docker python3-pip git -y && pip3 install -U awscli && pip install pyyaml==5.3.1 && pip3 install docker-compose


# Fix docker python wrapper 7.0 SSL version issue  
pip3 install docker==6.1.3

# Configure components
sudo systemctl enable docker && sudo systemctl start docker && sudo usermod -aG docker $USER

```

- Pull the MySQL Docker image:
Open a terminal and run the following command to download the official MySQL image:
- Create and run a MySQL container:
```bash
docker run -d \
  --name nlq-mysql \
  -p 3306:3306 \
  -e MYSQL_ROOT_PASSWORD=1234560 \
  -e MYSQL_DATABASE=llm \
  -e MYSQL_USER=llmdata \
  -e MYSQL_PASSWORD=llmdata \
  -v mysql-data:/var/lib/mysql \
  -v $(pwd)/initial_data:/opt/data \
  --restart always \
  mysql:8.0
```

- Verify the container is running:
```bash
docker ps
```

- Download script file and setup the database:
```bash
cd initial_data && wget https://github.com/fengxu1211/generative-bi-using-rag/raw/demo_data/application/initial_data/init_mysql_db.sql.zip

unzip init_mysql_db.sql.zip && cd ..

docker exec nlq-mysql sh -c "mysql -u root -p1234560 -D llm  < /opt/data/init_mysql_db.sql"
```


- To stop the container when you're done, use:
```bash
docker stop nlq-mysql
```

- To remove the container when you're done, use:
```bash
docker rm nlq-mysql
```

- To start it again later, use:
```bash
docker start nlq-mysql
```# text2sql_agent
