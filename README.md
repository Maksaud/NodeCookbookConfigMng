# Node

## Summary

### Node App

This app has multiple apps inside which includes a fibonacce calculator or a blog.

### CHEF

Chef is a software development kit delivering a set of tools for developing and testing Chef Cookbooks. It integrates the best of the breed tools created by Chef Community with Chef Client. Chef has a steep learning curve with lots of concepts and tools that have to be learned.

### Pre Requisites

- ChefDK
- git
- AWS credentials (for ec2 driver)

### Cookbook

This cookbook tests to see if these packages are installed, running and the app is listening on a proxy port:

#### Installs

```
nginx
NodeJS
pm2
```

#### Unit test

to run unit tests

```
chef exec rspec
```

Unit test is a level of testing of each individual components of the software.

These are what the unit tests look for

```
should install nginx
should enable the nginx service
should start the nginx serviceshould create a proxy.conf template in /etc/nginx/sites-available
should create a symlink of proxy.conf from sites-available to sites-enabled
should delete the symlink from the default config in sites-enabled
should install nodejs from a recipe
should install pm2 via npm
```

#### Integration Test

To run integration test

```
kitchen test
```

Integration test is the level of testing where certain components are combined with others and tested together and these are some of the intgration test for this cookbook

```
describe package ('nginx') do
  it { should be_installed }
end

describe service "nginx" do
  it { should be_running }
  it { should be_enabled }
end

describe port(80) do
  it { should be_listening }
end

describe http('http://localhost', enable_remote_worker: true) do
  its('status') { should cmp 502 }
end

describe package ('nodejs') do
  it { should be_installed }
  its('version') { should cmp > '8.11.2*' }
end

describe npm ("pm2") do 
  it { should be_installed }
end
```

To use to test on aws ec2 driver use kitchen_cloud.yml then kitchen test