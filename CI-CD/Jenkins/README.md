# Setting Up a Jenkins Job

## Prerequisites

1. **Jenkins Installed**: Ensure Jenkins is installed and accessible via a web interface.
2. **Jenkins User Permissions**: Make sure your Jenkins user has the necessary permissions to configure jobs and access the required repositories.
3. **GitHub Access**: You should have SSH credentials set up for GitHub to allow Jenkins to clone/push to repositories.

---

## Steps to Create and Configure a Jenkins Job

### 1. **Access Jenkins Dashboard**

   - Open your Jenkins web interface (e.g., `http://<your-jenkins-ip>:8080`).
   - Log in with your Jenkins credentials.

---

### 2. **Create a New Job**

   1. From the Jenkins dashboard, click **"New Item"**.
   2. Provide a **name** for the job (e.g., `my-first-job`).
   3. Select the type of job you want to create:
      - **Freestyle project**: Simple, easy-to-configure job.
      - **Pipeline**: More complex, allows integration with pipeline scripts.
   4. Click **OK** to proceed.

---

### 3. **Configure Source Code Management (SCM)**

   1. In the job configuration page, scroll down to **Source Code Management**.
   2. Select **Git**.
   3. Enter the **repository URL**:
      - If using SSH (recommended for private repositories), the URL should be in this format:
        `git@github.com:username/repository.git`
   4. For **credentials**, select the appropriate GitHub credentials (ensure that the private key is set up).
      - If you don't have credentials set up, click **Add** and enter your GitHub credentials.

---

### 4. **Configure Build Triggers**

   To automatically trigger the build based on events like code commits:

   1. Scroll down to **Build Triggers**.
   2. Select **GitHub hook trigger for GITScm polling** to trigger builds from GitHub webhook events.
   3. Alternatively, you can set up periodic builds using the **Build periodically** option.

---

### 5. **Configure Build Environment**

   To set up build environment variables, tools, or plugins:

   1. Scroll to **Build Environment**.
   2. Check **Use secret text(s) or file(s)** if you want to add environment variables like API keys or credentials.
   3. Check **Delete workspace before build starts** if you want to clear the workspace before each build.

---

### 6. **Configure Build Steps**

   1. Scroll to **Build**.
   2. Click **Add build step** and choose the appropriate action:
      - **Execute shell**: If you want to run a shell command or script. Example:
        ```bash
        echo "Building my app"
        npm install
        npm test
        ```

---

### 7. **Configure Post-build Actions**

   You can define post-build actions, such as notifications, archiving artifacts, etc.:

   1. Scroll to **Post-build Actions**.
   2. Choose actions like:
      - **Email Notifications**: Configure Jenkins to send an email upon build completion.
      - **Archive Artifacts**: Archive build output (e.g., `.tar`, `.zip` files).
      - **Deploy**: Deploy the code to a server or cloud service after the build.

---

### 8. **Save the Job**

   After all configurations are done, scroll to the bottom and click **Save**.

---

### 9. **Configure GitHub Webhook**

   To trigger Jenkins builds on commits to GitHub:

   1. Go to your repository on GitHub.
   2. Navigate to **Settings > Webhooks**.
   3. Click **Add webhook** and enter your Jenkins webhook URL in this format:
      `http://<jenkins-url>/github-webhook/`
   4. Set the content type to `application/json` and choose the events to trigger (e.g., **Push events**).
   5. Click **Add webhook**.

---

## Troubleshooting

- **Permission Issues**: Ensure Jenkins has the correct SSH key and GitHub credentials.
- **Git Pull Error**: If you get an error like `Permission denied (publickey)`, double-check your SSH keys in GitHub and Jenkins credentials.
- **Failed Builds**: Check the Jenkins build logs to identify issues in the build step or post-build action.

---

## 3 Job Pipeline:
# Jenkins Basics and Job Tasks

## Introduction to Jenkins

Jenkins is an open-source automation server that helps with continuous integration (CI) and continuous delivery (CD). It simplifies the automation of repetitive tasks such as building, testing, and deploying code. Jenkins is widely used in DevOps pipelines to automate and streamline the software development lifecycle.

### Key Features of Jenkins:
- **Automated Builds**: Jenkins can automatically build projects whenever changes are made to the source code repository.
- **Pipeline as Code**: Jenkins supports defining the build pipeline as code, which allows it to be version-controlled.
- **Plugins**: Jenkins has a rich ecosystem of plugins that can be used to integrate with various tools like Git, Docker, Kubernetes, and more.
- **Distributed Builds**: Jenkins can distribute tasks across multiple machines to optimize performance.

## The Job Tasks

In this section, we will walk through the steps that were performed in the Jenkins job, with a focus on resolving issues related to the SSH key for deploying the application.

### 1. **Cloning the Git Repository**

**Task Overview**: The job began by cloning a Git repository from GitHub using SSH credentials to access the repository. The repository used here is hosted on GitHub, and the private key is required to authenticate the connection.

- **What Happened**:
  - The `git` command was used to clone the repository, and an SSH key was required to access it.
  - Jenkins used the SSH Agent plugin to handle SSH authentication.
  - The private key used for authentication was configured as a Jenkins credential.

**Steps Involved**:
- Jenkins checked out the **latest commit** from the repository using the Git SSH URL.
- The **Git SSH** plugin was used to configure and fetch the repository, allowing the deployment pipeline to continue with the latest code.

### 2. **Deploying the Application**

**Task Overview**: After fetching the code, the next step was deploying the application to the target environment (a server). The deployment used **rsync** to transfer files over SSH to the remote machine.

- **What Happened**:
  - The `rsync` command was used to synchronize files from the Jenkins workspace to the target machine.
  - The process involved copying the application files to a remote server using SSH.
  - **SSH Authentication** was necessary to establish a connection and transfer files securely.

**Steps Involved**:
- **Error**: Initially, Jenkins was unable to find the SSH key file, which led to an error about the missing SSH key.
- **Solution**: The SSH Agent plugin was used to provide the SSH credentials and enable secure communication between Jenkins and the target server.
- The process successfully completed after configuring the correct SSH credentials.

### 3. **Post-Deployment: Running Scripts and Restarting the Application**

**Task Overview**: Once the code was deployed, a post-deployment script was executed. This script was responsible for tasks like seeding the database and restarting the application.

- **What Happened**:
  - After deployment, the **postinstall script** (`node seeds/seed.js`) was executed to run necessary database tasks like seeding the database.
  - **PM2** was used to manage the application processes, including restarting the application.

**Steps Involved**:
- **Seeding**: The `seed.js` file connected to the database and inserted initial data as required.
- **PM2**: The application was restarted using PM2, which is a process manager for Node.js applications. This ensured that the latest code changes were reflected in the live application.

---

## Key Concepts and Best Practices

### SSH Key Management in Jenkins
- When using Jenkins for tasks that require SSH (e.g., accessing GitHub or deploying to remote servers), the **SSH Agent plugin** is often used.
- The private key must be stored securely in Jenkins **Credentials** and not hardcoded into the pipeline script.
- The **SSH Agent** is responsible for managing the key and allowing Jenkins to authenticate securely without exposing sensitive key paths.

### Using Jenkins Pipelines for Deployment
- A Jenkins Pipeline is a set of automated tasks that define the steps needed to build, test, and deploy an application.
- It is best practice to define pipelines as code using a `Jenkinsfile` to version control the pipeline alongside the codebase.
- **Declarative Pipelines** are often used, as they provide a clear and structured way of defining the stages and steps involved in the pipeline.

### Troubleshooting SSH Issues
- **Error**: "Permission denied (publickey)" usually occurs when Jenkins cannot find the SSH private key, or it’s not properly configured.
- **Solution**: Ensure the SSH key is stored in Jenkins **Credentials** and used via the **SSH Agent** plugin, which will manage the key and inject it into the job for secure authentication.

### PM2 for Application Management
- **PM2** is a process manager for Node.js applications that allows you to run applications as background services, restart them, and monitor their status.
- After deploying new code, it’s important to restart the application to ensure the latest changes are reflected.
- Use **PM2** to ensure the application runs smoothly without manual intervention.

---

## Conclusion

By using Jenkins with the SSH Agent plugin, we can automate the process of building, testing, deploying, and managing applications. Configuring SSH authentication securely is essential for seamless integration and deployment. In this job, we successfully cloned the repository, deployed the code to the remote server, and ran the necessary post-deployment tasks. By leveraging Jenkins features like **SSH Agent**, **Git integration**, and **PM2**, we were able to automate and streamline the entire process.


# Jenkins 3-Job Pipeline and Deployment Process

## Introduction
A **3-job Jenkins pipeline** is a structured approach to **building, testing, and deploying** an application. This ensures **continuous integration and continuous deployment (CI/CD)** while maintaining high software quality and reliability.

---

## Jenkins 3-Job Pipeline Overview
The pipeline consists of three main jobs:

### 1. **Job 1: Build Job**
- Pulls the latest code from the GitHub repository.
- Installs necessary dependencies (`npm install` for a Node.js app).
- Runs unit tests to ensure code integrity.
- Packages the application (if needed).
- Stores artifacts for the next job.

### 2. **Job 2: Merge & Test Job**
- Merges the latest changes into the **development branch**.
- Runs integration tests to verify system behavior.
- Ensures that the new code does not break existing functionality.
- Prepares the application for deployment.

### 3. **Job 3: Deployment Job**
- Transfers the latest version of the application to the **EC2 instance**.
- Installs dependencies and required tools.
- Restarts necessary services (e.g., `nginx`, `pm2`).
- Ensures the application is running successfully.

---

## **Benefits of a 3-Job Pipeline**
1. **Modularization:** Separates build, test, and deployment phases for better management.
2. **Fault Isolation:** Fails fast, allowing issues to be identified early in the pipeline.
3. **Improved Reliability:** Ensures only tested and stable code reaches production.
4. **Automation:** Reduces manual intervention, increasing efficiency.
5. **Scalability:** Can be extended with additional testing or monitoring jobs.

---

## **Deployment Script Breakdown**
The following script is executed in **Job 3 (Deployment Job)**:

```sh
# Copy the application directory from Jenkins workspace to EC2 instance
scp -o StrictHostKeyChecking=no -r /var/jenkins/workspace/tech501-vytautas-job2-merge-plugin/app \
ubuntu@ec2-34-250-191-219.eu-west-1.compute.amazonaws.com:/repo/tech501-sparta-app/nodejs20-sparta-test-app/

# SSH into the EC2 instance
ssh ubuntu@ec2-34-250-191-219.eu-west-1.compute.amazonaws.com << 'EOF'

    # Verify that files have been copied successfully
    ls -l /repo/tech501-sparta-app/nodejs20-sparta-test-app/app

    # Navigate to the application directory
    cd /repo/tech501-sparta-app/nodejs20-sparta-test-app/app

    # List contents to confirm the presence of necessary files
    ls

    # Restart Nginx to ensure web service is active
    sudo systemctl restart nginx

    # Install application dependencies
    npm install

    # Install pm2 globally (as sudo to prevent permission issues)
    sudo npm install pm2 -g

    # Kill any running PM2 processes
    pm2 kill

    # Start the application using PM2
    pm2 start app.js

EOF
