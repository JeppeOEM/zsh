# Lecture 12: Continuous Deployment


Continuous Deployment, or CD (sometimes the term Continuous Delivery is used instead), takes advantage of continuous integration.
If we know that our product is stable and all tests passes, why not go ahead and deploy the product right away?

Continuous deployment is especially interesting in web development, because we can instantly deliver updates to the end user, whereas users on desktop or mobile platforms have to install the updates -- some users might see very frequent updates to such platforms as a sign of lack of quality assurance, but in web development they will probably not notice.

Keep in mind that documentation (including end user documentation) should be up-to-date.
It can be a bit of a challenge to align such things in larger projects where code and end user documentation are written by different people or organizational units.


## Some Initial Thoughts

When setting up continuous deployment, you should have a plan for when updates will be deployed.
One simple approach is to only update the `master` branch when we want to update production, and have other branches for daily integrations.

Furthermore, you should probably enforce branch rules to avoid unintended updates.


## What Continuous Deployment Must Cover

!!! note inline end

    In reality, things might be a bit more complicated.

    This is due to the fact that our updates might have introduced changes to the database schema, dependencies on other infrastructure, etc.[^0]
    However, as a product mature, these issues become less frequent, and we can usually deal with them as special cases.

[^0]: We can even handle many of these complications by running database migrations etc., in our `entrypoint`.

So we are in a situation where we know we have a version of our software product that passes tests and builds successfully, and we can go ahead and deploy our new version.

Let us try to list up the things that must happen in a web development project for the system to be updated in production:

- We must take down the running system[^1]
- We must update the running system with the new version
- We must bring the system back online

[^1]: There are actually ways to avoid this, but for things not to become overly complicated, we will ignore this for now.

If you think about it, you know how to do all these things:

- You can do a `docker-compose down`
- You can do a `docker pull myimage` or `git pull` to bring the local system up-to-date
- You can do a `docker-compose up`

And while we realize it might be more complicated, let us concentrate on this for now.


## Push Or Pull

There are fundamentally two ways we can do things:

- We can *push* the changes to the production server, e.g., `kea-dev`, from the CI-pipeline
- We can *pull* the changes from the CI-pipeline to the production server

In the latter case, we must somehow notify the production server that it needs to take action.


### Push

So let us say we go with the *push* way of updating our server.

We can make a new `continuous-deployment` stage in our CI-pipeline that will take care of pushing the updates.
But how?

Well, we can do the following:

- Generate an SSH key-pair (private and public key)
- Store the content of the *private key* as a masked environment variable on the CI-pipeline, just as we did with the `GITLAB_CI_TOKEN` in CI
- Add the *public key* to the `authorized_keys` file on the production server
- In the `continuous-deployment` stage echo the private key environment variable into the appropriate file within `.ssh/`
- Use the `ssh` command to remotely do the three tasks listed above, e.g., `ssh kea-dev ...`

There are pros and cons of this approach:

- Pro: it is the easiest approach
- Con: the process is fragile, i.e., it might break due to a simple network error, and leave the system down
- Con: from a security perspective, we might feel bad to give the CI-pipeline this kind of power over the production environment


### Pull

So let us look at the *pull* way of updating the server.

From the CI-pipeline, things gets very easy: we must simply *trigger* the production server to to pull the update.
This can be achieved in several ways:

- Via a secret URL
- Schedule a `cron` task to do the job
- Set an `at` job to do the task (see scheduling jobs below)

There are, again, pros and cons:

- Pro: much more robust, and unlikely to fail once triggered
- Pro: from a security standpoint this is better, because the CI-pipeline does not have power over the production server
- Con: bit more complicated


### Scheduling Jobs

Scheduling jobs is something we often need to do in IT, so we can have things happen automatically.

There are basically two scheduling systems on Unix-like systems:

- `at`: typically one-off jobs
- `cron`: typically reoccurring events

To run `at` jobs on Alpine Linux, we must do the following:

- Install the `at` package: `sudo apk add at`
- Register the `at daemon` with the *init* system: `sudo rc-update add atd`
- Have the init system start the daemon: `sudo rc-service atd start`
- Add user to `/etc/atd.allow`

Once this is done, you can have `at` run tasks for you:

- Run task in one minute: `echo 'touch killroy-was-here' >> killroy.txt | at now +1 minutes`
- List jobs: `atq`
- Remove job number `11`: `atrm 11`
- Show details for job `11`: `at -c 11`

If we are using `cron` instead, you could set up a job to check for updates once every hour:

- Run `crontab -e` and add this line: `0 * * * * check-for-updates.sh`


## Assignments

**Assignment 12.1:** Install the `at` scheduler and have it schedule some tasks. The jobs will run in their own non-interactive shell, so you will not be able to see the tasks run directly in your shell. Have the jobs do things to some test files so you can tell if they run successfully.

**Assignment 12.2:** For a simple project, try to implement continuous deployment via *push*.

**Assignment 12.3:** For a simple project, try to implement continuous deployment via *pull*.
