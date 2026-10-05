Troubleshooting Log

This is where I'm keeping track of problems I ran into while building the project, what caused them, and how I fixed them.

I want to keep these issues documented because they show what I actually learned while working through the project.

Phase 1 — Flask Application
ModuleNotFoundError: No module named 'app'

What happened:

When I first ran:

pytest -v


pytest had trouble importing app from my test file.

What caused it

My pyproject.toml file was originally in the test/ directory instead of the project root.

Because of that, pytest wasn't reading the configuration I expected it to use.

How I fixed it

I removed the incorrect configuration file and created pyproject.toml in the project root.

The configuration includes:

[tool.pytest.ini_options]
pythonpath = ["."]
testpaths = ["test"]


After moving the configuration file, pytest was able to find the application correctly.

What I learned

Configuration files need to be in the location where the tool expects to find them.

When a configuration doesn't seem to be working, I should check whether the tool is actually reading the configuration file.

Phase 2 — Git & GitHub
403 Permission denied when pushing to GitHub

What happened:

When I tried to push my repository, GitHub returned a 403 error.

The message looked similar to:

remote: Permission to ... denied
fatal: unable to access ...

What caused it

Git had an old or incorrect GitHub credential saved.

Even after creating a new Personal Access Token, Git was still trying to use the old credential.

How I fixed it

I cleared the saved GitHub credential:

echo "url=https://github.com" | git credential reject


Then I tried the push again and entered the correct credentials.

What I learned

If GitHub authentication keeps failing even after creating a new token, the problem can be the cached credential rather than the new token.

GitHub token permissions

What happened:

A new token still resulted in a 403 error when I tried to push.

What caused it

The token didn't have the required repository permissions.

How I fixed it

I created a token with the appropriate repository access and used it when pushing to GitHub.

What I learned

A GitHub token needs the correct permissions. Having access to a repository doesn't automatically mean every token has permission to modify it.

GitHub Ruleset wasn't blocking pushes

What happened:

I created a ruleset for main, but I could still push directly to the branch.

What caused it

The ruleset wasn't actually targeting the main branch.

The ruleset showed that it applied to zero branches.

How I fixed it

I edited the ruleset and added:

main


to the branch targeting pattern.

I then tried pushing directly to main again.

This time GitHub rejected the push with a repository rule violation.

What I learned

Creating a rule isn't enough. I also need to make sure the rule is actually targeting the resource I want it to protect.

Phase 3 — Docker
Container name already in use

What happened:

When I tried to run:

docker run --name flask-test ...


Docker returned an error saying the container name was already being used.

What caused it

I already had a container named flask-test.

Even if the container isn't running, Docker still reserves the name.

How I fixed it

I stopped and removed the old container:

docker stop flask-test
docker rm flask-test


Then I created the container again.

What I learned

docker run creates a new container.

If I want to reuse the same name, I need to remove the old container first.

I can use:

docker ps -a


to see both running and stopped containers.

ps command not found inside the container

What happened:

I tried:

docker exec flask-test ps aux


but Docker returned an error saying:

exec: "ps": executable file not found in $PATH

What caused it

The python:3.12-slim image doesn't include many normal Linux utilities.

The slim image is intentionally kept small.

How I worked around it

Instead of using ps inside the container, I can use:

docker top flask-test


This lets me inspect the container processes from the host.

What I learned

A slim Docker image won't contain every Linux command I'm used to having available.

That's actually one of the benefits of a slim production image because it reduces unnecessary software and keeps the image smaller.

If I need extra debugging tools, I should use them temporarily rather than adding unnecessary packages to the production image.
