#A simple, scalable solution for storing and serving build artifacts
来源：https://opensource.com/business/15/8/pinrepo

![pinrepo](https://opensource.com/sites/default/files/styles/image-full-size/public/images/business/OSDC_BUS_ArchitectureOfParticipation_520x292.png)

At Pinterest, our mission is to help people discover things they love so they can live a more creative and fulfilling life. Pinterest engineering moves amazingly fast, with some of the major services being released twice a day. We strive to build and integrate every commit in our mainline, which translates into tons of build artifacts every day. Storing them reliably and serving them efficiently with consistent performance poses a great challenge to our speed of growth.

Pinrepo is our answer to that challenge. It's a simple yet scalable solution for storing and serving build artifacts such as Debian packages, maven jars, and pypi packages internally. We've been running Pinrepo in production for more than eight months, with consistent performance and virtually no maintenance.

Now we're making Pinrepo open source on GitHub. There you can find all of the configuration stanzas and instructions to recreate it for yourself. It also includes a pypi release tool to release and maintain pypi packages. Check it out and let us know what you think! And, feel free to contribute back with your customizations and improvements.

##Pinrepo features

* Simple: publish and store build artifacts in AWS S3 and serve with Nginx reverse proxy
* Extensible: easily add other format supports such as RPM
* Reliable: highly available Nginx cluster and AWS S3 service
* Scalable: Nginx layer scales horizontally, as AWS S3 backend is highly scalable
* DevOps-friendly: been running in production for eight months with virtually no maintenance
* Overcoming scaling challenges

The biggest challenge Pinrepo helped us solve was scalability—it allowed us to efficiently serve large amounts of data over time. For instance, scaling became an issue when building a major 36MB Python package 50 times a day (that's a total of 1.8GB a day and 162GB every three months). We've used different solutions before, but their performance generally deteriorated over time as the number of objects grew.

In addition to scaling, there were also service availability and data durability concerns. The solutions we used previously were based on a single host and disk, and they were slow and crashed often. While there were backup processes, they often broke without notice. Maintaining these services was a nightmare, and we needed a reliable service on top of a durable, highly scalable object storage.

We considered using Amazon S3 directly, but we found we couldn't use it to store and serve build artifacts because the repository clients couldn't talk to S3 if the bucket wasn't configured as publicly accessible. These types of AWS S3 requests need to be "signed" first, including a process to calculate and attach the HMAC based on some of the elements of the request.

The problem could be solved by simply adding a cluster of Nginx servers in front of AWS S3. We could in theory "fix" all the repository clients so they could talk to S3 directly, but it would require a lot of work and be hard to maintain and extend to other artifact formats. On the other hand, not only could the Nginx layer sign and forward the requests to AWS, but it could also provide a local cache for artifacts with a huge performance improvement. The Nginx layer is stateless and could be easily scaled up horizontally, so it solved the availability problem as well. Plus, there's no need for extra backup process.

Our answer: Pinrepo, our internal artifact repository.

##Introducing Pinrepo

We created a cluster of Nginx servers behind a load balancer, proxied all the requests to the backend AWS S3 service, and used the Nginx module ngx_set_misc to create the AWS service request signatures.

![Pinrepo architecture. Image from Baogang Song, Copyright.](http://opensource.com/sites/default/files/resize/images/life-uploads/pinrepo-blog2-520x390.jpg  )

Pinrepo architecture. Image from Baogang Song, Copyright.

##Publishing artifacts directly to S3

There are many ways to upload and maintain the artifacts metadata and layouts in Amazon S3, including using deb-s3 to upload and maintain the debian packages, and using maven plugin maven-s3-wagon for maven packages. We couldn't find any existing tool to upload Python packages to S3, so we wrote our own pypi-release to upload and maintain pypi packages.

##Simple is better

By publishing and storing the build artifacts directly to Amazon S3 and frontending it with a Nginx cluster, we simplified the technical stack and achieved scalability and durability at the same time with minimum maintenance needs.

##Future improvements

A simple UI with search support could be a feature to add, though various S3 tools can be used to find out what's available in the repositories. Take caution when adding extra features and don't make them part of the critical path, as to not jeopardize the reliability and scalability of the existing simple solution. Staging repository and package promotion are also nice to have, though not necessary if you have staging deploy and pre-production test environment. Pinrepo could also be extended to easily support more package formats such as RPM.

Acknowledgements: Thanks to Raj Patel, who helped evaluate various existing solutions and formalize the process; Jayme Cox for reviewing the design and advising on implementation details; and to all of the members of the Cloud Engineering team at Pinterest, which drives reliability, speed, and security for the site, and builds the technical building blocks for cloud infrastructure and developers

