`inventory` provides a new Puppet command which outputs a data structure
describing properties of the system on which it's run. Here's a quick
example:

```
puppet inventory all | head -n 20
{
  "schema_version": 1,
  "created": "2016-07-14T07:42:01Z",
  "resources": [
    {
      "title": "root",
      "resource": "group",
      "gid": 0
    },
    {
      "title": "bin",
      "resource": "group",
      "gid": 1
    },
    {
      "title": "daemon",
      "resource": "group",
      "gid": 2
    },
    {
```

Specifically `inventory` outputs information on:

* Users
* Groups
* Packages
* Services
* Facts

All of this information is already available via the RAL and `puppet
resource` or via `facter`. `inventory` simply offers a higher-level
interface, a single command that outputs everything in a simplified data
format.


## Installation

`inventory` is packaged as a Puppet Module, and can be installed from
the Forge like so:

    puppet module install puppetlabs-inventory

You can also include in your `Puppetfile` if using r10k or
librarian-puppet:

    mod 'puppetlabs/inventory'


## Demonstration

A simple way of demonstrating the module is to use Docker. Docker is
not required, it just makes for nice single line examples. The following
should be run from a checkout of this repository.


Output inventory information for the `puppet/puppet-agent-alpine` image
from Hub:

    docker run --rm -it -v $(pwd):/src:ro -e RUBYLIB=/src/lib puppet/puppet-agent-alpine inventory all

Output the same information but instead of formatted JSON render the
output as YAML:

    docker run --rm -it -v $(pwd):/src:ro -e RUBYLIB=/src/lib puppet/puppet-agent-alpine inventory all --render-as yaml

Only generate the resources portion of the output:

    docker run --rm -it -v $(pwd):/src:ro -e RUBYLIB=/src/lib puppet/puppet-agent-alpine inventory resources

Only generate the facts portion of the output:

    docker run --rm -it -v $(pwd):/src:ro -e RUBYLIB=/src/lib puppet/puppet-agent-alpine inventory facts

The above examples all use images which already have Puppet installed on
them. This example runs an inventory of the vanilla `centos` image,
which does not have Puppet installed. It does that by mounting Puppet
from another container as described in the [Puppet-in-Docker
examples](https://github.com/puppetlabs/puppet-in-docker-examples/tree/master/puppet-at-runtime):

    docker run --name puppet-agent -v /opt/puppetlabs puppet/puppet-agent-ubuntu --version
    docker run --rm -it --volumes-from=puppet-agent -v $(pwd):/src:ro -e RUBYLIB=/src/lib centos /opt/puppetlabs/bin/puppet inventory all


## Status and Schema Version

This is an early prototype at the moment and based on experience with
writing clients for the generated format the structure of the data
produced may change a lot between now and a 1.0 release of the module. I
will increment the `schema_version` key in the output if/when I make
changes but until the module hits a 1.0 release don't expect any
backwards compatibility guarantees.


## Maintainers

This repository is maintained by: Gareth Rushgrove <gareth@puppet.com>.