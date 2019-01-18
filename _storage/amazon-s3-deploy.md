---
layout: storage
permalink: /:collection/:path.html
---
# Configure a hub on Amazon S3
{:.no_toc}

This teaches you how to run a Gaia hub on Amazon S3 (DO). Amazon S3 is an affordable and convenient cloud server provider. This example uses Amazon S3 instance for file storage.

* TOC
{:toc}

<div class="uk-card uk-card-default uk-card-body">
<h5>Is this tutorial for you?</h5>

<p>This documentation is appropriate for advanced power users who are familiar with command line tools, <code>ssh</code>, and basic editing configuration files.</p>

<p>If you are planning on running an <emphasis>open-membership hub</emphasis> or an <emphasis>application-specific hub</emphasis>, see <a href="hub-operation.html">the section on Hub Operation</a>.</p>

</div>

## Prerequisites you need

This procedure uses Amazon AWS to choose and configure an Amazon Machine Image
(AMI) running a Gaia service. For this reason, you should have an AWS account
on the <a href="https://aws.amazon.com/free/" target="\_blank">Amazon AWS free
tier</a>, personal account, or corporate account.  These instructions assume you
are using a free tier account.

These instructions assume you have already created a free <a
href="https://www.freenom.com" target="\_blank">domain through the freenom
service</a>. If you have another domain, you can use that instead.

Finally, setting up the SSL certificates on your EC2 instance requires you to use the terminal command line on your workstation. Make sure you have the `watch` command installed using the `which` command.

```
$ which watch
/usr/local/bin/watch
```

If `watch` is not located, install it on your workstation.

## Task 1: Launch an EC2 instance

1. Visit the <a href="https://aws.amazon.com/free/" target="\_blank">AWS Free Tier page</a> and choose **Sign in to the Console**.

   ![](/storage/images/aws-console.png)

2. Make sure your region is set to `us-west-2` which is **Oregon**.

   ![](/storage/images/us-west-2.png)

3. Choose **Launch a virtual machine**.

   The system opens the EC2 dashboard.

4. Enter `Gaia` in the search bar.

   The system finds AMIs in the Marketplace and the Community.

5. Choose **Community AMIs**.

   The system displays the available Gaia hub images.

   ![](/storage/images/gaia-community.png)

6. Select the **Blockstack.Gaia.Hub_1549643256 - ami-04793ff666176c1f3** image.

   The system displays **Step 2: Choose an Instance Type** page.

    ![](/storage/images/tier-2-image.png)

7. Select **t2.micro** and choose **Configure Instance Details**.

   1. Select a VPC.

       A default VPC is created with a free tier account. You can use this
       default VPC. Or you can choose another VPC. If you choose another VPC,
       ensure the `Subnet` value is set to a subnet reachable by a public IP.

       {% include important.html content="If you're using a private subnet, you
       should attach an elastic IP (EIP) to the VM. This EIP allows you to
       reboot the instance without worrying whether the address will reset. To
       attach an IP, <strong>press allocate new address</strong> and follow the
       instructions to attach the EIP to your new EC2 instance." %}

   2. Set **Protect against accidental termination**.

      If you terminate a Gaia instance, you lose all the data associated with it. Protection adds an extra step to terminating your Gaia instance.

   3. Display the **Advanced Details**.

      At this point, you are going to configure environment variables for your instance.  

   4. Paste the following into the **Advanced Details**.

      ```json
      {
        "ignition": { "version": "2.2.0" },
        "storage": {
          "files": [{
            "filesystem": "root",
            "path": "/etc/environment",
            "mode": 420,
            "contents": {
              "source": "data:application/octet-stream,DOMAIN%3D<DOMAIN_NAME_VALUE>%0ASTAGING%3D<STAGING_VALUE>"
            }
          }]
        }
      }
      ```

      A value of `0` to requests a valid SSL certificate. At this point, you'll
      request a staging certificate (`0`).

   5. Replace `<DOMAIN_NAME_VALUE>` with your domain name.

      For example, `maryhub.ml` is my domain name.

   6. Replace the `<STAGING_VALUE>` with the number `0`.

      At this point, your instance should look like the following:

      ![](/storage/images/ec2-instance-details.png)      

8. Choose **Next: Add Storage**.

   The default storage options should be sufficient.

9. Choose **Next: Add tags**.
10. Add a **Key** of `purpose` with the value `gaia`.

    ![](/storage/images/tag-add.png)    

11. Choose **Next: Configure Security Group**.
12. Create a security group with the following three types:

    <table class="uk-table uk-table-small uk-table-divider">
      <tr>
        <th>Type</th>
        <th>Protocol</th>
        <th>Port Range</th>
        <th>Source</th>
        <th>Description</th>
      </tr>
      <tr>
        <td>SSH</td>
        <td>TCP</td>
        <td>22</td>
        <td>My IP</td>
        <td>optional</td>
      </tr>
      <tr>
        <td>HTTP</td>
        <td>TCP</td>
        <td>80</td>
        <td>Anywhere</td>
        <td>optional</td>
      </tr>
      <tr>
        <td>HTTPS</td>
        <td>TCP</td>
        <td>443</td>
        <td>Anywhere</td>
        <td>optional</td>
      </tr>
    </table>

13. Choose **Review and Launch**.

    The system may warn you that the selection is not free tier eligible. You can ignore this for now.

14. Press **Launch**.

    The system prompts you for a key pair.

15. Select **Create a new keypair** or **Choose an existing key pair**.
16. Select **Launch Instances**.

    The system launches your instance.

    ![](/storage/images/aws-launch-status.png)    

During the instance launch, the machine image on the instance performs some initial setup processes. These processes take a few minutes depending on the minutes, typically thought it should not take more than 5 mins. While this is happening your **Status Checks** reflect the **Initializing** status.


## Task 2: Test your Gaia server

Now, you are ready to test your Gaia server and make sure it is up and running.

1. Visit the <a href="https://aws.amazon.com/free/" target="\_blank">AWS Free Tier page</a> and choose **Sign in to the Console**.

   ![](/storage/images/aws-console.png)

2. Choose **All services > EC2**.

   The system displays the **EC2 Dashboard**.

   ![](/storage/images/ec2-dashboard.png)    

3. Select **Running Instances**.

   The system displays your running instances.

4. Locate your recently launched Gaia server.

   Make sure the instance shows as running and **Status Checks** are complete.
   Completed status checks ensures the Gaia processes and service were started.

5. Select the **Description** tab.

   ![](/storage/images/ec2-instance.png)   

6. Locate the **IPv4 Public IP** value.
7. Copy the IP and paste it in your browser.

   You should see a message that your connection is not private.

8. Press **Advanced**.

   ![Hub test](/storage/images/private-connection.png)

9. Choose to proceed.
10. Extend the IP with the `PUBLIC_IP/hub_info` tag like so.

    You should see a response from your Gaia hub!

    ![Hub test](/storage/images/aws-hub.png)

    At this point, you should see a **Not secure** message in the browser. That's because you haven't yet enabled SSL certification.


## Task 3: Configure a domain name

At this point, you can point a domain to your Gaia hub. Although it's not required, it is highly recommended. If you use a domain, you can migrate your instance to a different server (or even provider such as Azure or Dropbox) at any time, and still access it through the domain URL. Just point your domain at the IP address for your EC2 instance. Use an `A Record` DNS type.

These instructions assume you have already created a free <a href="https://www.freenom.com" target="\_blank">domain through the freenom service</a>. To point this freenom domain to your Gaia hub, do the following:

1. Log into your freenom account.
2. Choose the **Manage Freenom Domain** tab.
3. Add an **A** record leave the **Name** field blank.

   This points your entire domain to the hub IP.

4. Save your changes.

5. Create a CNAME record.

   For example, you can use the prefix `www` with your domain name. When you are done, your

6. Save your changes.

   At this point, your DNS management should look similar to the following except that with your domain rather than the `maryhub.ga` domain.

   ![DNS fields](/storage/images/aws-dns-fields.png)

7. After your changes propagate, visit your new domain at the `hub_info` page.

   ![Domain test](/storage/images/domain-test.png)

At this point, you have the following. An EC2 instance running Gaia and a DNS
record pointing your domain to this instance.


## Task 4: Set up your SSL certificates

In this section you setup your SSL certificates.  These steps require you to interact from a workstation command line with your running EC2 instance.

1. Open a terminal on your local workstation.
2. Confirm the hub DNS is set correctly with the following command:

   ```bash
   watch -n 2 -t -g -x host <domain>
   ```

   Substitute your domain name for the `<domain>` variable. For example:

   ```bash
   watch -n 2 -t -g -x host maryhub.ga
   maryhub.ml has address 34.219.71.143
   ```

   If the command returns the correct IP, the same as appears on your EC2 dashboard, stop the process with a` CTRL-C` on your keyboard.

3. Change the permissions on your downloaded `.pem` file.

   For example, this

   ```
   chmod 400 <location-of-pem>
   ```

4. SSH from your workstation and restart it.

   This process requires that you know the location of the `.pem` file you downloaded when you created the keypair.

   ```
   ssh -t -i <your keyfile.pem> -A core@<public ip address> "sudo systemctl restart get-acme-certs.service"
   ```

   For example:

   ```
   $ ssh -t -i /Users/manthony/gaia.pem -A core@34.219.71.143 "sudo systemctl restart get-acme-certs.service"
   Connection to 34.219.71.143 closed.
   ```

5. Refresh or renter the domain name fo your Gaia hub.

   The *Not secure* message should no longer appear in the browser bar.

6. Check the SSL certificate for your hub.

   Each browser has its own check procedure, for example, Chrome:

   ![](/storage/images/cert-check.png)     


## Tips and tricks

Once your Gaia storage hub is up and running on AWS, you may occassionally need to troubleshoot. This section contains some useful information for interacting with your EC2 instance.

To SSH to the EC2 host directly:

```bash
ssh -t -i <your keyfile.pem> core@<public ip address>
```

On the host, you'll find certificates are stored in the
`/gaia/docker/nginx/certbot/conf/live/<domain>` directory.

The services that implement and run Gaia setup on the instance are located in
the `/etc/systemd/system` directory. The service file has the following content.

```
[Unit]
Description=Install LetsEncrypt Certs
Requires=gaia-hub.service
[Service]
Type=oneshot
WorkingDirectory=/gaia/docker
EnvironmentFile=/etc/environment

RemainAfterExit=yes
ExecStart=/bin/bash -x /gaia/docker/nginx/certbot/letsencrypt.sh

[Install]
WantedBy=multi-user.target
```

It reads the `/etc/environment` file which shows the `DOMAIN` location and the
type of `STAGING` value taken from the EC2 **Advanced Details** section.  To
regenerate certificates:

1. Edit the `/etc/environment` file.

   ```
   sudo vi /etc/environment
   ```

2. Change the values in the file.

   If `STAGING` value is set to `1`, the service generates a staging certificate for localhost. Staging certificates are not valid for production, use them only for testing. A value of `0` generates a valid  certificate for use in production.

3. Restart the `get-acme-certs.service` service.

   ```
   $ sudo systemctl restart get-acme-certs.service
   ```

You can run `sudo systemctl restart get-acme-certs.service` at anytime to
regenerate valid certifications.
