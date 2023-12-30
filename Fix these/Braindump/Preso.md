

Hello everyone, i would like to welcome you to this talk

Today we will be discussing about feature flags in terraform. 

Before we begin, I would like to introduce myself. i am Prabesh Thapa and i have been working in field of SRE/DevOps for quite some time now. 

The key topics that we will be discussing in this talk are 

1. What are feature flags and why we should use them

2. How to implement feature toggles in terraform

3. Pass feature flags from configuration file

4. and a quick demo

So what is terraform ?

Terraform is an API driven infrastructure as code tool created by Hashicorp. 

It uses declarative code using language called HCL.

As we all know that feature flags have been implemented in software engineering quite sometime now. 

Let's talk a little bit about them.

So what are feature flags ?

It is a software engineering technique which allows us to enable or disable features without additional code.

They are also known as feature toggles or switches

There are various benefits of using feature flags like running rollout or roll back operation or allowing developers to test code in a controlled environment

Feature flags are passed from either configuration files, environment variables or dedicated feature flag management platforms like launchdarkly.

Next, we will discuss about why we should use it in infrastructure as code ?

The first reason  is increased flexibility: 

Feature flags allows granular control over the rollout of changes, and can be used to turn specific features or functionality on or off for different environments or user groups. 

This allows for more efficient and flexible development, testing, and deployment of any specific feature.

You can selectively provision resoure based on their environment 

that is 

you can have smalller instanc on dev but large and more capable instances on prod. 


Using feature flags in IasC, it helps to improve scalability

As we know Feature flags can be used to enable or disable certain features or functionality based on the current load on your infrastructure. 

Now what this does is, it allows us to scale infrastructure up or down as necessary, without the need to make changes to code improving scalability in general
    
    
Using feature flag we get better control over deployments using technique such as blue green, canary etc. We can Test and validate changes in a controlled environment, and quickly rollback changes if necessary.
    
It also helps to improved safety by reducing the risk of introducing breaking changes. 

This helps to ensure that your infrastructure is always in a stable and working state.
    
Using feature flags as part of infrastructure configuration, we are able to perform effective monitoring and debugging because using feature flags we are aware which changes were introduced incrementally and knowledge of this allows us to isolate issues to specific features or functionality hence improving debugging.


Let's look into how we can achieve this

The key player in this game of feature flag in terraform is `count` meta argument.

It allows us to create specified number of identical resource.

Here in the example we have a resource blog to create AWS instance.

You can see in count argument that we are asking terraform to provision 4 identical AWS instances.

One key thing to remember here is, we can pass `0` value to `count` as well. Which means if we set `count` to `0` then the resource will not be created at all.

Another key player in this story is conditional expressions.

Conditional expressions in terraform uses values of a boolean expression to selcet one of two values

```
condition ? a : b
```

If condition is true, then result is a and if condition is false, then result is b

Using `count` and `conditional expressions` we can implement feature toggles in terraform. 

Let's look into some implementations.

First we will look into creating environment based feature toggles. 

To do that, we will first create a variable named env and  then we use that variable along with count and conditional expression in resource blog. 

Here i have two resouce blocks, one to create a puny or small vm and another to create beefy vm based on environment.

If the value of env is dev then value of count in `puny_vm` is set to 1 and hence puny vm is created

and if the value of env is prod then value of count in beefy_vm is set to 3 and hence 3 instances of beefy vm is created.

We will look this in demo as well.

Now let's look into how we can create feature toggle based on specific value to toggle specific resource. 

What we can do is similar to the things we discussed before,

we will create a local variable to enable that flag, and then pass it to the resource just like we did for env based toggles.

The working mechanism is same but instead of relying on environment, here rely of a specific feature flag to enable or disable creation of that resource.

Another way you can implement feature flags are at module level.

We create a flag for that module and then pass it directly to the module instead of passing it at resource level. This has been possible after terraform 0.13 which was a good leap forward.

Usage is similar to implementing feature flag in resource, we create a dedicated flag but instead of passing to the resource, we pass flag directly to module and 

if the flag is set, it will provision that instance.

There are a'lot of ways you could leverage things we discussed like blue green deployment using floating IPs in digital ocean or perform canary release using AWS load balancer target groups.

I mean possibilities are endless


Let's talk about how to organise these flags. There are various ways but the two most used ways are

defining feature flags in local variables. We saw how to do this in previous slides as well.

We define flags as part of local variable and them use them as needed.

Another way we can organise feature flags is using configuration file. This is my personal favourite as we can get complete information about the infra without having to dig down deep into variables or terraform configuration files. 

Here we define variables in configuration file,

then load those variables into terraform and use them as needed. Its kinda neat  right. 

Anyways lets move on to the demo

demo video....

So that's all the demo.

Before we end this talk, here are the key takeaways i want to repeat again which are

feature flag is a very powerful technique which has been implemented in software engineering for long time. 

I think its time we start using it as part of infrastructure as code as well

as it help us add flexibility, scalability and maintainability to the code.

Here is the link to the repository of the code shown in the demo


and Here are the references i used to prepare for this talk.

Thank you for listing.  I hope this talk was useful

I would love to connect with you all if you are interested and talk about terraform, feature flags and DevOps in general.

Here are the links to my socials. Thanks again and enjoy the rest of the conference.
