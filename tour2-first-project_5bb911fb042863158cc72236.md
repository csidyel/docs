To build, test and deploy your code on Semaphore, you'll need to
create a project for it to live in.

## Creating a project in the web interface

Once you're inside an organization that you've created or were invited to,
you're ready to create a CI/CD project.

Follow the `Projects > New` link in the sidebar of Semaphore web interface.
You'll go through a simple two-step process:

1. Select a Git repository;
2. Commit a configuration file and start your first workflow. You will be able
   able to edit the provided example configuration file before creating a
   commit.

## Alternative: creating a project from command line

You can also create and manage projects using the Semaphore Command Line
Interface (CLI):

1. [Install sem CLI][install-cli];
2. Run `sem init` in the root of your repository;
3. `git push` a new commit to run your first workflow.

`sem init` creates a deploy key and webhook on GitHub, so
that Semaphore can access your code as it changes. It also creates a pipeline
definition file `.semaphore/semaphore.yml` in the repository on your computer.
In case that file already exists, sem will not overwrite it.

After you commit `.semaphore/semaphore.yml` and run `git push`, you should see
the pipeline running in your browser. You can also see all running jobs in your
terminal via `sem get jobs`.

## Moving on

Congratulations! You've successfully created your first project,
and initialized it with a working pipeline.
Take some time to explore the Semaphore web interface, and compare what you
see with the generated YAML file.

[Let's move on to an overview of key Semaphore concepts][next] to learn what
each part means.

#### Troubleshooting

In case running `sem init` throws an error:

```
error: http status 422 with message
"{"message":"POST https://api.github.com/repos/orgname/projectname/keys: 404 - Not Found // See:
https://developer.github.com/v3/repos/keys/#add-a-new-deploy-key";}"
received from upstream
```

or

```
"{"message":"admin permisssions are required on the repository in order to add the project to Semaphore"}"
```

You can do the following:

- Check if the user who wants to add a project to Semaphore is a member of the
  given Semaphore organization, and has Admin-level permissions for the
  repository on GitHub.
- Check if the access for Semaphore 2.0 was granted within your GitHub
  organization. You can do that [here][github-connection].

[next]: https://docs.semaphoreci.com/article/62-concepts
[install-cli]: https://docs.semaphoreci.com/article/53-sem-reference
[github-connection]: https://github.com/settings/connections/applications/328c742132e5407abd7d
