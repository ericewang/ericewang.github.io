---
layout: post
title: "KubeCon 2017 Thoughts"
published: true
tags: kubernetes cloud-native conference
---

Earlier this month, I went to KubeCon/CloudNativeCon 2017 in Austin, Texas. I was fortunate enough to give a talk there about some of the cool work ([Real Security for Services
on Kubernetes](https://schd.ws/hosted_files/kccncna17/0d/KubeCon-Slides-Databricks.pdf)) my team focuses on. Beyond that, I attended many talks, visited sponsor showcases, and chatted with a few attendees to get a better picture of the cool things going on. It gave me clear perspective on the current state and direction of one of the today's most popular open source projects.
<!--more-->

### Conference Background Info

Kubernetes was open sourced in 2014, and was later donated to the [Cloud Native Computing Foundation](https://www.cncf.io/) (CNCF) in 2015. The CNCF has grown to house [most popular cloud infrastructure open source projects](https://www.cncf.io/projects/) like Prometheus, Fluentd, and Envoy. Now they hold an annual joint conference to bring together over four thousand technologists and professionals across industries to discuss Kubernetes and other cloud native projects. During the 3-day conference, at least 60% of talks were focused on Kubernetes specifically. The next most-popular topic was service meshes, and there was a long tail of remaining topics.

## Overall themes of the conference

I'll discuss a few of my key takeaways from my experience over the week. There were a ton of other cool topics that were discussed, but I don't have space (or the memory) to cover them all and do them justice.

### Kubernetes is the future

As of *very* recently, all major cloud providers support for managed Kubernetes. The cloud provider Big 3 has Microsoft [Azure Container Service (AKS)](https://azure.microsoft.com/en-us/services/container-service/), Amazon [Elastic Kubernetes Service (EKS)](https://aws.amazon.com/eks/), and [Google Kubernetes Engine (GKE)](https://cloud.google.com/kubernetes-engine/). Other clouds (like Alibaba, IBM, Oracle) support Kubernetes as well, and many companies provided their own managed Kubernetes service (like CoreOS's Tectonic and Red Hat's OpenShift). There were at least 30 other companies sponsoring the conference, with business models such as providing better tooling around the Kubernetes resources (i.e. visualization of cluster state, system logging for security auditing) or straight-up technology consulting! It's ~~amazing~~ ridiculous to see the level of adoption that people have raced towards with Kubernetes, and it's pretty easy to say that Kubernetes has won the market for cloud-agnostic container orchestration. The parent companies of similar platforms like Docker Swarm and Mesos have added support for Kubernetes in their enterprise offerings, proving the shift in the customer desires.

However, even though Kubernetes has risen to the top in popularity, the goal of its well-designed infrastructure isn't that every developer should be aware of it.

### Infrastructure/Kubernetes should be invisible/boring

The goal for platforms/SREs is to keep things working, but while operating as quietly as possible (i.e target as many 9's as you can for your SLA's). The more aware other teams are of its existence, the worse of a job its doing. Kubernetes fits in that same category. Developers working on top of your platform don't want to know how exactly their software is being delivered and executed, whether packaged as a container or deployed to a platform like Kubernetes. That interface should be simple, requiring little cognitive overhead, so that they can focus on their areas of expertise instead. This can be in the form of CI/CD pipelines that automatically deploy your test branch to Kubernetes and build your production containers for release (like in this [talk](https://www.youtube.com/watch?v=07jq-5VbBVQ)). Brendan Burns, one of the creators of Kubernetes, demoed a new proof-of-concept library called [Metaparticle](https://metaparticle.io/), that allows developers to define everything needed to successfully run an application (including packaging/runtime) in the same language and file that they are familiar with. This is an important reminder that at the end of the day, as an infrastructure/dev-ops engineer, the end goal is efficacy/stability of the organization. Kubernetes is just a tool to help make that easier.

### Service Meshes are _also_ the future

Tons of new projects have come out in the service mesh space, with proxies like [Envoy](https://www.envoyproxy.io/)/[Linkerd](https://linkerd.io/) and service mesh control planes like [Istio](https://istio.io/)/[Conduit](https://github.com/runconduit/conduit) that operate in conjunction. Service meshes provide a networking wrapper over services to set of built-in enhancements to each service, removing the individual developer burden of correctly implementing common networking/monitoring boilerplate. Once most/all services are incorporated into the service mesh, teams can take advantage of the major benefits: observability (distributed tracing, better telemetry), advanced routing (dynamic load balancing), and network level security access control.

There's a huge network effect benefit when it comes to service mesh (as expected by the name). However, there are still benefits for an organization to make the transition from monolithic app to partial mesh. Matt Klein, creator of Envoy, gave a [talk](https://www.youtube.com/watch?v=IeJDjq-COjk) about his experience with migrating Lyft's architecture from a PHP monolith to fully Envoy integrated infrastructure with separate services (and still a legacy monolith). It demonstrates the potential that existing companies have in making the journey towards Service Oriented Architecture, with checkpoints along the way to get value.

### Cloud Native Interfaces need to be standardized

Currently, there's many competiting tools in each separate space in infrastructure. Docker and Rkt are now both options for container runtimes, with pretty difference architectures. Kubernetes introduced the [Container Runtime Interface](http://blog.kubernetes.io/2016/12/container-runtime-interface-cri-in-kubernetes.html), which makes it easier for future projects to BYO implementation, without needing to fork your own version of Kubernetes and slowly drift apart. Standards like [OpenTracing](http://opentracing.io/) and Istio are designed with that same idea in mind, as you can choose to use your favorite tracing framework/visualization tool and sidecar proxy. These systems have to be designed this way to make adoption easier, as legacy systems will always have their unique one-off situations, with a legacy ingress controller, logging system, stateful services, etc. They won't be able to rip and replace everything in their system, so an all-or-nothing package isn't an option.

### Kubernetes needs to be pluggable
Open source software brought a new landscape to the evolution of software. If you find an issue or want to extend functionality, all you have to do is issue a pull request upstream to contribute. Still, this process can be slow, back and forth in code review, with your change eventually being incorporated in the next version released (likely taking months). In contrast, the app/containers ecosystem is much flexible, with marketplaces like npm or DockerHub to quickly share your content. In its most recent releases of 1.7/1.8, Kubernetes has stepped up its game by introducing new ways to build addons without modifying core Kubernetes code. [Custom Resources Definitions](https://kubernetes.io/docs/concepts/api-extension/custom-resources/) give users a new way to add declarative resources that are tightly coupled with Kubernetes in the same way typical resources are defined. These resources can be acted upon by custom controllers, and tools like [kube-metacontroller](https://github.com/GoogleCloudPlatform/kube-metacontroller)(note: just a proof-of-concept) make manipulating infrastructure like writing ordinary scripts against an API. These tools significantly reduce the complexity of building new features on top of Kubernetes, and with the help of platforms for sharing these ideas (like what [Helm](https://helm.sh/) is building), the next major change may not come from core Kubernetes.

## Wrap-up

It's amazing to see the force behind this community. Currently, there isn't much standardization of the tools people use to enhance their experience with working with Kubernetes, so I'm very curious to see what emerges over the next few years.
