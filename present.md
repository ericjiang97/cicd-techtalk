<!-- $theme: default -->

<center>
<h1>Automating Deployments with CI & CD Using BitBucket Pipelines</h1>

<h3>Maintained by</h3>

<img src="https://avatars3.githubusercontent.com/u/5687681?v=4&s=460" 
      style="width: 10%; border-radius: 100%"/>
### Eric Jiang

###### _This presentation's code/slides can be found on [https://github.com/lorderikir/cicd-techtalk](https://github.com/lorderikir/cicd-techtalk)_

<div>
  	<img class="custom-footer-1" src="https://www.atlassian.com/dam/jcr:e2a6f06f-b3d5-4002-aed3-73539c56a2eb/Bitbucket@2x-blue.png" style="bottom: 0; height: 35px" />
    <img class="custom-footer-1" src="https://www.chef.io/wp-content/uploads/2017/03/google-cloud-platform-1024x246.png" style="bottom: 0; height: 70px"/>
</div>

<span style="font-size: 15px">Slide Deck Version: v1.0.0</span>
</center>

<span style="font-size: 15px">Memes Warning: You might see many memes in this talk</span>

---

# So what is this talk about?

1. Introduction to BitBucket Pipelines
2. Branch Workflow and Setting up Permissions
3. Automating Testing with Pipelines
4. Automating Deployments to Google App Engine
5. Building advanced pipelines

---

# Introduction to BitBucket Pipelines

---

# First of all, what is Pipelines?

<center>
  <img class="custom-footer-1" src="https://cdn-images-1.medium.com/max/1200/0*XWjbrBTJV8a8C2PQ." style="width: 70%" />
</center>

>Integrated CI/CD for Bitbucket Cloud that’s trivial to set up, automating your code from test to production. 
> _BitBucket Site (Atlassian)_

---

<center>
  <img src="https://image.slidesharecdn.com/devopsandaws-codepalousa-170608135154/95/devops-and-aws-code-palousa-2017-17-638.jpg?cb=1496930240" />
  <h4>So how to do it?</h4>
</center>



---

# monPlan Git Workflow
<center>
  <img src="https://i.imgur.com/c5L8RFE.png" width="10000%"/>
</center>

- **master**: branch is the key branch, everytime for release
- **develop**: _unstable_, most of the PRs should go here
- **'feature/*'**, **'fix/***, etc.: are 'for purpose' branches, these branches are for development
- **deploy** (not shown), is for **manual** deployments to prod

---

# Branch Permissions
* **no-one** has write permissions to _master_, _develop_ or _deploy_, not even DevOps or Admin.
* **everyone** has PR write access, only when
	- 1 Tests Pass
	- 1 Approval from a Code-review 
* that way, we 'silly'-proof our codebase.

---

## Setting Branch Permissions

Branch permissions by people who have _administrator_ privellages inside a repo.

To set this, Go to Settings and under workflow select Branch Permissions.

<center>
  <img src="https://cdn-images-1.medium.com/max/800/1*PayEoXsUD1eJ0b3xRbZdfg.png" style="width: 75%"/>
</center>

---

## Demo of Branch Permissions

---
<center>
  <h1>Automating Testing</h1>
  <img src="https://www.barrykooij.com/wp-content/uploads/2014/12/bf321947675de864ac980948a6c835a82768a0eb48091d32e432fabcf4165666.jpg" />
</center>



<span style="font-size: 15px">Note that we will be using <b>NodeJS</b> for the sake of this demo/talk, but the idea is similar</span>

---

<center><h1>First, It's the Development Team's Problem to write <u>appropriate</u> Tests, not the CI/CDs role</h1><h1>Second, the CI works on an alternative CLI, so running tests on interactive mode <u>won't work</u></h1></center>

---

# Setting up CI for Testing

_default Pipeline runs for all branches (if not matched)_

_from bitbucket-pipelines.yml_
```yaml
image: node:8

pipelines:
  default:
    - step:
        caches:
          - node
        script: 
          - yarn # <- Install Dependencies
          - yarn test:ci # <- Test in Non-CI Mode
```

---


---