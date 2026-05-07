# Day 22

## Task

The DevOps team established a new Git repository last week, which remains unused at present. However, the Nautilus application development team now requires a copy of this repository on the Storage Server in the Stratos DC. Follow the provided details to clone the repository:

    The repository to be cloned is located at /opt/ecommerce.git

    Clone this Git repository to the /usr/src/kodekloudrepos directory. Perform this task using the natasha user, and ensure that no modifications are made to the repository or existing directories, such as changing permissions or making unauthorized alterations.

## Solution

```bash
cd /usr/src/kodekloudrepos/
git clone /opt/ecommerce.git/
```

## Validation

```bash
ls -la /usr/src/kodekloudrepos/
```

## Insights

Another one done in a couple seconds, but this is actually the first time I've ever cloned from a local repo rather than one on GitHub.