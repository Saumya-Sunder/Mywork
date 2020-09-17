# CORTX Development Quickstart Guide

This guide is intended to provide quick start instructions for developers who want to build, test, and contribute to the CORTX software.  If you are merely looking to _test_ the software, please use [these instructions](doc/CORTX_on_Virtual_Appliance.rst).

To contribute to the CORTX Open-Source project, you'll need to understand the way the CORTX repository is organized. 

The CORTX repository is the first and the parent repository that contains top-level documentation about the CORTX Community. The CORTX-Motr repository contains Motr files and is the second component of the CORTX project. Motr is the central component that stores Objects and Key-Values. The S3 Server component is built on Motr and all component related information is posited in the CORTX-S3 Server repository. To test and develop the basic functionality of CORTX, only these two components are required.  However, the full CORTX software stack includes additional components such as web interfaces and automated provisioning software.  Therefore, in this page we include the instructions for both testing and developing the basic functionality using just S3 and motr as well as instructions for testing and developing the entire CORTX system. 

# Let's get CORTX ready!

**Before you Begin**

- You'll need to [Build and Test your VM Environment](doc/BUILD_ENVIRONMENT.md).
 
- Ensure that you have RoCE—RDMA over Converged Ethernet and TCP connectivity.

⚠️ **Known Limitation**

The CORTX stack currently does not work on Intel's OmniPath cards.
***

Since CORTX is composed of the Motr and S3 Server components, you'll need to:

1. [Set up CORTX-Motr](https://github.com/Seagate/cortx-motr/blob/dev/doc/Quick-Start-Guide.rst)

2. [Get CORTX-S3 Server ready](https://github.com/Seagate/cortx-s3server/blob/dev/docs/CORTX-S3%20Server%20Quick%20Start%20Guide.md)

Watch our CORTX Engineer, Justin Woo, demonstrate **<link to the video>** the process.
***

## Contribute to CORTX Community
***

## Code of Conduct

Thanks for joining us and we're glad to have you. We take community very seriously and we are committed to creating a community built on respectful interactions and inclusivity as documented in our [Contributor Covenant Code of Conduct](https://github.com/Seagate/cortx/blob/main/CODE_OF_CONDUCT.md). 

You can report instances of abusive, harassing, or otherwise unacceptable behavior by contacting the project team at opensource@seagate.com.

Refer to the CORTX [Community Guide](doc/CORTXContributionGuide.md) that hosts all information about community values, code of conduct, how to contribute code and documentation, community and code style guide, and how to reach out to us. 

We excited for your interest in CORTX and hope you will join us. We take community very seriously and we are committed to creating a community built on respectful interactions and inclusivity as documented in our [Code of Conduct](CODE_OF_CONDUCT.md).

## Contribution Guide

- [**Code of Conduct**](#Code-of-Conduct)
- [**Contribution Process**](Contribution-Process)
- [**Submitting issues**](#Submitting-Issues)
- [**Contributing to Documentation**](#Contributing_to_Documentation)

## Contribution Process

<details>
<summary>Prerequisites</summary>
<p>

- Please read our [CORTX Code Style Guide](../main/doc/CodeStyle.md).

- Get started with [GitHub Tools and Procedures](../doc/Tools.rst), if you are new to GitHub.

- Before you set up your GitHub, you'll need to

  1. Generate the SSH key on your development box using:

     ```shell
     $ ssh-keygen -o -t rsa -b 4096 -C "Email-address"
     ```
  2. Add the SSH key to your GitHub Account:
    1. Copy the public key: `id_rsa.pub`. By default, your public key is located at `/root/.ssh/id_rsa.pub`
    2. Navigate to [GitHub SSH key settings](https://github.com/settings/keys) on your GitHub account.
      
    :page_with_curl:**Note:** Ensure that you've set your Email ID as the Primary Email Address associated with your GitHub Account. SSO will not work if you do not set up your Email ID as your Primary Email Address.
    
    3. Paste the SSH key you generated in Step 1 and select *Enable SSO*.
    4. Click **Authorize** to authorize SSO for your SSH key.
    5. [Create a Personal Access Token or PAT](https://help.github.com/en/github/authenticating-to-github/creating-a-personal-access-token).

     :page_with_curl:**Note:** Ensure that you have enabled SSO for your PAT.
      
 - Update Git to the latest version. If you're on an older version, you'll see errors in your commit hooks that look like this:

    `$ git commit`

     **Sample Output**
  
    ```shell

    git: 'interpret-trailers' is not a git command.
    See 'git --help'
    cannot insert change-id line in .git/COMMIT_EDITMSG
    ```

- Install Fix for CentOS 7.x by using: `$ yum remove git`
  * Download the [RPM file from here](https://packages.endpoint.com/rhel/7/os/x86_64/endpoint-repo-1.7-1.x86_64.rpm) and run the following commands:
  
    ```shell
       $ yum -y install
       $ yum -y install git
    ```

   </p>
    </details>

Contributing to the CORTX repository is a six-step process where you'll need to:

1. [Set up Git on your Development Box](#1-Setup-Git-on-your-Development-Box)
2. [Clone the CORTX Repository](#2-Clone-the-CORTX-Repository)
3. [Commit your Code](#3-Commit-your-Code)
4. [Create a Pull Request](#4-Create-a-Pull-Request)
5. [Run Jenkins and System Tests](#5-Run-Jenkins-and-System-Tests)
6. [Sign CLA and Pass DCO](#Sign-CLA-and-Pass-DCO)

### 1. Setup Git on your Development Box

Once you've installed the prerequisites, follow these steps to set up Git on your Development Box.

<details>
  <summary> Click to expand!</summary>
  <p>

1. Install git-clang-format using: `$ yum install git-clang-format`

2. Set up git config options using:

   ```shell

   $ git config --global user.name ‘Your Name’
   $ git config --global user.email ‘Your.Name@domain_name’
   $ git config --global color.ui auto
   $ git config --global credential.helper cache
   ```
</p>
</details>

### 2. Clone the CORTX Repository

<details>
<summary>Click to expand!</summary>
<p>

Before you can work on a GitHub feature, you'll need to clone the repository you're working on. **Fork** the repository to clone it into your private GitHub repository and follow these steps:

1. Navigate to the repository homepage on GitHub.
2. Click **Fork**
3. Run the following commands in Shell:
   
   `$ git clone git@github.com:"your-github-id"/repo-name.git`

4. You'll need to setup the upstream repository in the remote list. This is a one-time activity. Run the following command to view the configured remote repository for your fork.
    
   `$ git remote -v`  

    **Sample Output:**
    
    ```shell
    
     origin git@github.com:<gitgub-id>/cortx-sspl.git (fetch)
     origin git@github.com:<github-id>/cortx-sspl.git (push)
     ```

 5. Set up the upstream repository in the remote list using:
   
    `$ git remote add upstream git@github.com:Seagate/reponame.git`
      
    `$ git remote -v`

     **Sample Output:**
    
     ```shell
    
     origin git@github.com:<gitgub-id>/cortx-sspl.git (fetch)
     origin git@github.com:<github-id>/cortx-sspl.git (push)
     upstream git@github.com:Seagate/cortx-sspl.git (fetch)
     upstream git@github.com:Seagate/cortx-sspl.git (push)
     ```
    
6. Check out to your branch using:

   `$ git checkout "branchname"`

   `$ git checkout -b 'your-local-branch-name`
   
    :page_with_curl: **Note:** By default, you'll need to contribute to the main branch. However, this differs for various repositories as shown below:
    
   | **Repository**   | **Branch Name** | 
   | :----------------| :-------------: |
   | cortx-s3server   | main            | 
   | cortx-hare       | dev             | 
   | cortx-motr       | dev             |
   | cortx-prvsnr     | main            |
   | cortx-sspl       | dev             |

</p>
</details>

### 3. Commit your Code 

<details>
<summary>Click to expand!</summary>
<p>

:page_with_curl: **Note:** At any point in time to rebase your local branch to the latest main branch, follow these steps:

  ```shell

  $ git pull origin master
  $ git submodule update --init --recursive
  $ git checkout 'your-local-branch'
  $ git pull origin 'your-remote-branch-name'
  $ git submodule update --init --recursive
  $ git rebase origin/master
  ```
  
You can make changes to the code and save them in your files.

1. Use the command below to add files that need to be pushed to the git staging area:

    `$ git add foo/somefile.cc`

2. To commit your code changes use:

   `$ git commit -s -m ‘comment’` - enter your GitHub Account ID and an appropriate Feature or Change description in comment.

3. Check out your git log to view the details of your commit and verify the author name using: `$ git log`

   :page_with_curl:**Note:** If you need to change the author name for your commit, refer to the GitHub article on [Changing author info](https://docs.github.com/en/github/using-git/changing-author-info).

4. To Push your changes to GitHub, use: `$ git push origin 'your-local-branch-name'`

   **Sample Output**

   ```shell

   Enumerating objects: 4, done.
   Counting objects: 100% (4/4), done.
   Delta compression using up to 2 threads
   Compressing objects: 100% (2/2), done.
   Writing objects: 100% (3/3), 332 bytes | 332.00 KiB/s, done.
   Total 3 (delta 1), reused 0 (delta 0)
   remote: Resolving deltas: 100% (1/1), completed with 1 local object.
   remote:
   remote: Create a pull request for 'your-local-branch-name' on GitHub by visiting:
   remote: https://github.com/<your-GitHub-Id>/cortx-s3server/pull/new/<your-local-branch-name>
   remote: To github.com:<your-GitHub-Id>/reponame.git
   * [new branch] <your-local-branch-name> -> <your-local-branch-name>
   ```
</p>
</details>

### 4. Create a Pull Request 

<details>
<summary>Click to expand!</summary>
  <p>       
   
1. Once you Push changes to GitHub, the output will display a URL for creating a Pull Request, as shown in the sample code above.

   :page_with_curl:**Note:** To resolve conflicts, follow the troubleshooting steps mentioned in git error messages.

2. You'll be redirected to GitHib remote.
3. Select the relevant repository branch from the *Branches/Tags* drop-down list.
4. Click **Create pull request** to create the pull request.
5. Add reviewers to your pull request to review and provide feedback on your changes.

</p>
</details>

### 5. Run Jenkins and System Tests

Creating a pull request automatically triggers Jenkins jobs and System tests. To familiarize yourself with jenkins, please visit the [Jenkins wiki page](https://en.wikipedia.org/wiki/Jenkins_(software)).

### 6. Sign CLA and Pass DCO 

<details>
  <summary>Click to expand!</summary>
  <p>

#### CLA

In order to clarify the intellectual property license granted with Contributions from any person or entity, CORTX Community may require a Contributor License Agreement (CLA) on file that has been signed by each Contributor, indicating agreement to the license terms below. This license is for your protection as a Contributor as well as the protection of the project and its users; it does not change your rights to use your own Contributions for any other purpose.

#### DCO

DCO is always required. The code reviewers will use the [decision tree](https://github.com/Seagate/cortx/blob/main/doc/dco_cla.md) to determine when CLA is required.
To ensure contributions can be redistributed by all under an open source license, all contributions must be signed with [DCO](https://opensource.com/article/18/3/cla-vs-dco-whats-difference). To further ensure that all members of the community can redistribute and resell CORTX should and when they so choose, [CLA may be required on a case-by-case basis](https://github.com/Seagate/cortx/blob/main/doc/cla/README.md) such that corporations cannot attempt to prevent others from reselling CORTX.

You can pass DCO in many ways:

- While creating a Pull Request via the GitHub UI, add `Signed-off-by: "Name" <email address>` in the PR comments section. 

   **Example:** `Signed-off-by: John Doe <John.doe@gmail.com>`

- DCO will automatically pass if you push commits using [GitHub Desktop](https://docs.github.com/en/desktop/installing-and-configuring-github-desktop/configuring-git-for-github-desktop). 

- You can pass DCO by adding a Signed-off-by line to commit messages in Git CLI:

   `Signed-off-by: Random J Developer <random@developer.example.org>`
   
    Git even has a `-s` command line option to append this automatically to your commit message:

   `$ git commit -s -m` - here -m is your commit message.
  
</p>
</details>

### The GitHub Triage Process

========
Triaging
========

Triaging is about prioritizing and troubleshooting issues raised by you in `GitHub <https://github.com/>`_. Triage can broadly be defined as a process oriented approach towards issue resolution and conflict management.

*******************
Process of Triaging
*******************
The process of triaging in CORTX is depicted in the diagram below.


 .. image:: images/IMS.png
 
Creating an Issue
=================
Perform the below mentioned procedure to create an issue in GitHub.

1. Login to GitHub with your credentials.

2. Navigate to the CORTX repository. Then, click **Issues**. List of issues are displayed.

3. If there are multiple issue types, click Get started next to the type of issue you'd like to open.

4. Click **New Issue**. A page requesting the **Title** and **Description** is displayed.

5. Enter a title and description for your issue, and click **Submit new issue**.

**Note**: Click **Open a blank issue** if the type of issue you want to open, is not included in the available different types of issues.

## Contributing to Documentation TODO

## Resources 

Refer to these Quickstart Guides to build and contribute to the CORTX project.

<details>
<summary>Click to expand!</summary>
<p>

- Provisioner
- Motr
- S3 Server
- CSM

- Know more about [CORTX CI/CD and Automation](doc/CI_CD.md).
- Setup [CORTX on JBOD](https://github.com/Seagate/cortx/blob/main/doc/scaleout/READ_ME.rst).
- Learn more about the [CORTX Architechture](doc/architecture.md).
- You can [submit requests and bugs using GitHub Issues](https://github.com/Seagate/cortx/issues).

</p>
</details>

## Communication Channels

- Join the CORTX Slack Channel [![Slack](https://img.shields.io/badge/chat-on%20Slack-blue")](https://join.slack.com/t/cortxcommunity/shared_invite/zt-femhm3zm-yiCs5V9NBxh89a_709FFXQ?) and chat with your fellow contributors.
- For any questions or clarifications, mail us at [cortx-questions@seagate.com](cortx-questions@seagate.com)
- You can start a thread in the [Github Community] (Link TBA) for any questions, suggestions, feedback, or discussions with your fellow community members. 

### Thank You!

We thank you for stopping by to check out the CORTX Community. We are fully dedicated to our mission to build open source technologies that help the world save unlimited data and solve challenging data problems. Join our mission to help reinvent a data-driven world.
