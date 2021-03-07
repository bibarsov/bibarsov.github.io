---
published: true
layout: post
date: 2021-02-28T00:00:00.000Z
categories: others
---
## Fetching image metadata from Gitlab Registry ##

### Motivation ###
I arrived at this idea when I was creating first implementation of continuous integration flow.
My pipeline pushes docker image (service artifact) to gitlab registry and afterwards it fetches that image 
and deploys it in our kubernetes cluster. <br> 
But there were lack of information what commit we used to build that image. 
That should be the single source of truth solution, not just tag of image (for example we can't determine commit hash from the `latest` tag for sure).

### Implementation ###
Docker allows to add some metadata to an image using "LABEL".
That's what we are using in our solution:

```dockerfile
FROM foobar

ARG COMMIT_SHA=unspecified
LABEL project_commit_sha=$COMMIT_SHA

```
You see, we defined the argument in our Dockerfile and its value is used to set the label.<br>
We provide this value following way:<br>
`docker build -t some_image_name --build-arg COMMIT_SHA=sOmeComMiThaSh .`

It was pretty easy up to this point, and now the real fun begins.

Why did we decide to fetch labels from gitlab-registry?
Few reasons:
* Kubernetes [doesn't allow](https://github.com/kubernetes/kubernetes/issues/47368) to inspect docker image labels through its api 
* Getting to docker environment of kubernetes is cumbersome
* Gitlab-registry's API provides this ability

In light of the above I created bash script that goes step-by-step in order to get that `project_commit_sha` label value.
Briefly it does:
1. Obtaining jwt token by Gitlab Access Token
2. Getting manifest of image of type `application/vnd.docker.distribution.manifest.v2+json`
3. Getting image metadata by fetching blob metadata layer
4. Parsing that metadata and extracting the value

Source code of script is [here](https://gist.github.com/bibarsov/10e2f35b26ed47b35535567e90cff11d). Enjoy it.