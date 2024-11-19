# _Secure URL Hosting with AWS: Route 53, Load Balancer, EC2, and Certificate Manager Integration_

## AWS VPC, Subnets, Internet Gateway, Route Table, and EC2 Setup  

### 1. Create VPC  
1. Navigate to the **VPC Console** in AWS.  
2. Click **Create VPC** and provide the following details:  
   - **Name:** `test-vpc`  
   - **CIDR Block:** `12.0.0.0/16`  
3. Click **Create**.  

### 2. Create Subnets  
1. In the **VPC Console**, go to **Subnets** and click **Create Subnet**.  
2. Add the following details for the first subnet:  
   - **Name:** `test-public-subnet-1-1a`  
   - **VPC:** `test-vpc`  
   - **CIDR Block:** `12.0.1.0/24`  
   - **Availability Zone:** `1a` (or your preferred zone).  
3. Repeat the process for the second subnet:  
   - **Name:** `test-public-subnet-1-1b`  
   - **CIDR Block:** `12.0.2.0/24`  
   - **Availability Zone:** `1b` (or your preferred zone).  
4. Click **Create**.

### 3. Attach Internet Gateway  
1. In the **VPC Console**, go to **Internet Gateways**.  
2. Click **Create Internet Gateway** and provide the following:  
   - **Name:** `test-igw`  
3. Attach the internet gateway to the VPC:  
   - **VPC:** `test-vpc`.  

### 4. Configure Route Table  
1. Go to **Route Tables** in the **VPC Console**.  
2. Create a new route table:  
   - **Name:** `test-rt-public`  
   - **VPC:** `test-vpc`  
3. Add the subnets to the route table:  
   - Associate `test-public-subnet-1-1a`.  
   - Associate `test-public-subnet-1-1b`.  
4. Edit the routes:  
   - Add a route for **internet access**:  
     - **Destination:** `0.0.0.0/0`  
     - **Target:** `test-igw`.  

### 5. Launch an EC2 Instance  
1. Navigate to the **EC2 Console** and click **Launch Instance**.  
2. Provide the following details:  
   - **Name:** `test-ec2` (or create multiple instances as per requirement)
   - **VPC:** `test-vpc`  
   - **Subnet:** Choose `test-public-subnet-1-1a` or `test-public-subnet-1-1b`.  
   - **Auto-assign Public IP:** Enabled.  
3. Configure security group to allow:  
   - **SSH Traffic:** Port 22  
   - **HTTP Traffic:** Port 80  
   - **HTTPS Traffic:** Port 443  
4. Add user data script if required.  
5. Click **Launch**.  

### 6. Create a Target Group  
1. Go to the **Target Groups** section under **EC2 Console**.  
2. Create a new target group:  
   - **Type:** Instances  
   - **Name:** `test-tg`  
   - **VPC:** `test-vpc`  
3. Add the EC2 instance to the target group (status will be pending).  
4. Click **Create**.  

You now have a VPC with public subnets, an internet gateway, a route table for internet access, and an EC2 instance ready for traffic!

## Configure Load Balancer, Route 53, and HTTPS with AWS Certificate Manager (ACM)

### 1. Configure the Load Balancer (ALB)  
1. Go to the **EC2 Console** and navigate to **Load Balancers**.  
2. Create an **Application Load Balancer (ALB)** with the following settings:  
   - **Name:** `test-lb`  
   - **VPC:** `test-vpc`  
3. Create a new security group:  
   - **Name:** `test-sg-for-lb`  
   - **VPC:** `test-vpc`  
   - Allow **SSH**, **HTTPS**, and **HTTP** traffic.  
   - Save and exit.  
4. Back in the load balancer configuration, select the newly created security group.  
5. Add the target group:  
   - **Target Group Name:** `test-tg`.  
6. Click **Create** to finalize the load balancer.


### 2. Configure Route 53  
1. Go to the **Route 53 Console** and create a new hosted zone:  
   - **Domain Name:** `testdomain.com`. **NOTE: THIS IS DUMMY DOMAIN, YOU CAN YOU WHATEVER YOU WANT.**
2. Copy the **name servers (NS)** provided by AWS and add them to **Cloudflare** or your domain registrar.  
3. In **Route 53**, create a record:  
   - **Routing Policy:** Simple routing.  
   - **Record Type:** A (Alias to Application and Classic Load Balancer).  
   - **Alias Target:** Select the load balancer (`test-lb`).  
   - Save and create the record.  
4. Test the URL in your browser:  
   - Open `http://testdomain.com`.  
   - **Note:** It won't run over HTTPS yet.


### 3. Request an SSL Certificate via ACM  
1. Navigate to **Certificate Manager** and request a new certificate:  
   - Select **Request a Public Certificate**.  
   - Add the FQDN: `testdomain.com`.  
   - Select **DNS Validation** (since the domain is already pointed to AWS nameservers).  
   - Click **Request**.  
2. Once the certificate is created, it will be in **Pending Validation** status.  
3. Open **Certificate Manager**:  
   - Go to your certificates.  
   - Click on your certificate and select **Create Records in Route 53**.  
   - Select the domain and create the required DNS record.  
4. The certificate will automatically validate once the DNS changes propagate.


### 4. Add HTTPS Listener to the Load Balancer  
1. Go to the **EC2 Console** and navigate to **Load Balancers**.  
2. Select `test-lb` and add a new listener:  
   - **Protocol:** HTTPS  
   - **Port:** 443  
   - **Forward To:** `test-tg` (your target group).  
   - **Secure Listener Settings:**  
     - **Certificate Source:** Select ACM.  
     - **Certificate Name:** `testdomain.com`.  
3. Click **Add** to apply the listener.  
4. Test the URL in your browser:  
   - Open `https://testdomain.com`.  
   - At this point, the URL will be accessible over both HTTP and HTTPS.

### 5. Redirect HTTP to HTTPS  
1. Go to the **Load Balancer** in the **EC2 Console**.  
2. Select `test-lb` and edit the **HTTP:80** listener rule:  
   - In the **Default (Listener Rules)** section, select **Actions > Edit Rule**.  
   - Update the rule with the following:  
     - **Routing Action:** Redirect to URL.  
     - **Protocol:** HTTPS.  
     - **Port:** 443.  
   - Save the changes.  
3. Test the URL again:  
   - Both `http://testdomain.com` and `https://testdomain.com` will redirect to HTTPS.

You have successfully secured your application using AWS services and redirected all traffic to HTTPS!










