# Node

## Summary

### Node App

This app has multiple apps inside which includes a fibonacce calculator or a blog.

### CHEF

Chef is a software development kit delivering a set of tools for developing and testing Chef Cookbooks. It integrates the best of the breed tools created by Chef Community with Chef Client. Chef has a steep learning curve with lots of concepts and tools that have to be learned.

### Pre Requisites

- NodeJS Cookbook
- Chef license - accept
- npm
- git

### Cookbook

This cookbook tests to see if these packages are installed, running and the app is listening on a proxy port:

#### Installs

```
nginx
NodeJS
pm2
```

#### To run test

to run unit tests
```
chef exec rspec
```

To run integration test

```
kitchen test
```

To use to test on aws ec2 driver use kitchen_cloud.yml then kitchen test

#### Unit test

```
describe 'node::default' do
  context 'When all attributes are default, on Ubuntu 16.04' do
    let(:chef_run) do
      # for a complete list of available platforms and versions see:
      # https://github.com/customink/fauxhai/blob/master/PLATFORMS.md
      runner = ChefSpec::SoloRunner.new(platform: 'ubuntu', version: '16.04')
      runner.converge(described_recipe)
    end

    it 'converges successfully' do
      expect { chef_run }.to_not raise_error
    end

    it 'runs apt get update' do
      expect(chef_run).to update_apt_update 'update_sources'
    end

    it 'should install nginx' do
      expect(chef_run).to install_package 'nginx'
    end

    it 'should enable the nginx service' do
      expect(chef_run).to enable_service 'nginx'
    end

    it 'should start the nginx service' do
      expect(chef_run).to start_service 'nginx'
    end

    it 'should create a proxy.conf template in /etc/nginx/sites-available' do
      expect(chef_run).to create_template("/etc/nginx/sites-available/proxy.conf").with_variables(proxy_port: 3000)
    end

    it 'should create a symlink of proxy.conf from sites-available to sites-enabled' do
      expect(chef_run).to create_link("/etc/nginx/sites-enabled/proxy.conf").with_link_type(:symbolic)
    end

    it 'should delete the symlink from the default config in sites-enabled' do
      expect(chef_run).to delete_link "/etc/nginx/sites-enabled/default" 
    end

    it "should install nodejs from a recipe" do
      expect(chef_run).to include_recipe("nodejs")
    end

    it 'should install pm2 via npm' do
      expect(chef_run).to install_nodejs_npm('pm2')
    end
  end
end
```

#### Integration Test
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