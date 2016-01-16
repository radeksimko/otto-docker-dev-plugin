# otto-docker-dev-plugin

Otto plugin for Docker development environment.

At the moment this is just proposal.

## Motivation

**Vagrant** does have docker support, but it's not very flexible for multi-container apps
(which if you follow the 12-Factor manifesto is the majority).
**Vagrant** does not expose dockerd to be easily accessed from outside of the VM
as opposed to **Docker Machine**.

**Docker Machine** exposes dockerd configuration via `docker-machine env`,
but **Machine** isn't as mature as **Vagrant** in terms of networking, port forwarding, file sharing etc.
`docker-machine env` also introduces different issues
as environment variables are short-lived in this context and unavailable across the system.
Most importantly **Machine** doesn't allow any codification of the environment
(**Vagrant** does so via `Vagrantfile` which is a Ruby DSL).

**Docker Compose** (formerly Fig) solves the multi-container problem nicely (as opposed to **Vagrant**)
by letting users to define each container in `docker-compose.yml`
and then control these via `docker-compose up/kill`.

https://github.com/mitchellh/vagrant/issues/6600

## Goal

Have `docker-compose.yml` in project root and be able to run `otto dev`
and then just test the service via browser or 
to spin up all the containers, expose ports to the host and share volumes.

## Expected limitations

For both file shares & port forwarding, there's chain of dependency between
`Host <-> VM (Vagrant) <-> Docker container`.
We can get around this by statically sharing the current workdir,
which Vagrant does by default anyway and also statically
forwarding a specific port range from the VM to the host.
Such port range may be then used in `docker-compose.yml` as host ports.

It would be nice to have dynamic allocation of both, but Virtualbox doesn't seem
to be able to change such things at runtime (not sure about other VM providers).
