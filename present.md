<!-- $theme: default -->

<center>
<h1>Automating Development with Git Workflows and CI/CD</h1>

<h3>Maintained by</h3>

<img src="https://avatars3.githubusercontent.com/u/5687681?v=4&s=460" 
      style="width: 10%; border-radius: 100%"/>
### Eric Jiang

###### _This presentation's code & slides can be found on [https://github.com/lorderikir/cicd-techtalk](https://github.com/lorderikir/cicd-techtalk)_

<div>
  	<img class="custom-footer-1" src="https://www.atlassian.com/dam/jcr:e2a6f06f-b3d5-4002-aed3-73539c56a2eb/Bitbucket@2x-blue.png" style="bottom: 0; height: 35px; margin-right: 100px" />
    <img class="custom-footer-1" src="https://i.imgur.com/2QHwIqo.png" style="bottom: 0; height: 35px"/>
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

>Integrated CI/CD for Bitbucket Cloud that's trivial to set up, automating your code from test to production. 
> _BitBucket Site (Atlassian)_

---

<center>
  <img src="https://image.slidesharecdn.com/devopsandaws-codepalousa-170608135154/95/devops-and-aws-code-palousa-2017-17-638.jpg?cb=1496930240" />
  <h4>So how to do it?</h4>
</center>



---

# Gitflow
- At monPlan we use a method called Gitflow - https://datasift.github.io/gitflow/IntroducingGitFlow.html

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

Branch permissions are created by people who have _administrator_ privellages inside a repo.

To set this, Go to Settings and under workflow select Branch Permissions.

<center>
  <img src="https://cdn-images-1.medium.com/max/800/1*PayEoXsUD1eJ0b3xRbZdfg.png" style="width: 75%"/>
</center>

---

<center>
<h2>The reason why we do this, is so that this can't happen</h2>
<img src="
https://i.imgflip.com/27rmfp.jpg" />
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
# Use Prebuilt Node 8 Image (Node 8 supports yarn)
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

_We can also run workflows on branches_

|      Pattern         |    Targets    |
|----------------------|---------------|
| _actual branch name_ | actual branch |
| feature/*            | all branches with `feature/` such as `feature/sso`, `feature/login`, etc. |
| * | Matches all branches, tags, or bookmarks. The star symbol (`*`) must be in single quotes. Doesn't match those with slash |
| ** | Matches all branches including those which have slash |


---

So, simple we can run it with the `branches` mode

```
# Use Prebuilt Node 8 Image (Node 8 supports yarn)
image: node:8
 

pipelines:
  default:
    - step:
        caches:
          - node
        script: 
          - yarn # <- Install Dependencies
          - yarn test:ci # <- Test in Non-CI Mode
  branches:
  	# run branches here
```

---

What if we want to have testing on certain branches, such as `develop`.


```yaml
# .... (reduced for verbosity)
  branches:
  	- step:
          name: Run Unit Tests # <- We can name steps
          caches:
            - node
          script:
            - yarn # <- Install Dependencies
            - yarn test:ci # <- Test in Non-CI Mode
```

---

<center>
  <h1>An example of a pipeline (incl. deployments)</h1>
  <img src="https://i.imgur.com/238y8To.png" />
</center>

---

# Now, we can automate deployments.

<img src="http://electric-cloud.com/wp-content/uploads/2014/09/deliver-continuously1.png" />

---

# Automating Deployments to Google App Engine

---

# We can write our script for delivery.
1. Create Service Account with Details, Create Private Key and Download as JSON
2. Encode JSON under base64 and upload as an environment variable in repo settings
3. Pipeline deployment script will need to install Google Cloud SDK first
4. (Build site - ReactJS) and deploy using `google app deploy`

---

# Automated vs Manual (One-click) Deployments

You can also set up automatic vs manual deployments/steps

```
# run tests and deploy to PROD
deploy:
      - step:
          caches:
            - node
          script: 
            - yarn
            - yarn test:ci
      - step:
          name: Deploy to Prod
          deployment: staging
          # you can also switch trigger to 'automatic'
          trigger: manual 
          caches:
            - node
          script:
            # Install Google App Engine SDK
            # deploy scripts below
```

---

# When should you do type of deployments


---

# Designing a good pipeline with Workflows

- Designing a good pipeline is really important as it allows integrated workflow within branches
- Using something like Gitflow, instead of running CI during each Pull Request, every PR triggers a build
- Then when a PR is created, the PR cannot be pulled in unless the previous tests have passed. (Really Handy!)
---

# Playing with other Pipelines Settings (such as scheduling)

- Pipelines can also be scheduled
- To do this, go to Pipelines, then click schedules and add a new one
- monPlan is configured to run tests at Close of Business (COB, 5pm) every day on **master** and **develop** branches
- Automated deployments to **QAT** (Early Preview - eap) _every Wednesday at 2pm_

---
