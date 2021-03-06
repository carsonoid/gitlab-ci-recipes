### 0. Add User

``` sh
sudo mkdir gitlab_ci
sudo pw useradd gitlab_ci -s /usr/sbin/nologin -c "Gitlab CI User"
sudo chown gitlab_ci:gitlab_ci gitlab_ci
```

### 1. Required Packages

Install using your favourite port manager (i.e. portmaster). Needed ports (from install dir `/usr/ports/`) are

- ftp/wget  
- ftp/curl  
- textproc/libxml2  
- textproc/libxslt  
- databases/redis  
- devel/icu  
- devel/readline  
- devel/git  
- textproc/libyaml

NB: This list may not be complete.

### 2. Installing RVM for gitlab_ci user


``` sh
cd /usr/home/gitlab_ci
sudo -u gitlab_ci bash
curl -L https://get.rvm.io | bash -s stable --ruby
echo "source /usr/home/gitlab_ci/.rvm/scripts/rvm" >> ~/.bashrc
exit
```

### 3. only tested with [PostgreSql](../postgres/install.md)

### 4. works as mentioned in original install instructions

### 5. Setup

``` sh
# Act as gitlab_ci user
sudo -u gitlab_ci bash
cd ~/gitlab-ci

# Create a tmp and sockets directory inside application
mkdir -p tmp/pids
mkdir tmp/sockets

# Install dependencies
gem install bundler

# For Postgres, you install w/o the mysql gem, otherwise just remove it from the following command :
bundle --without development test mysql

```

If Bundle fails at installing gem `rugged` then [try raising the version level](https://github.com/alexdo/gitlab-ci/commit/459e2fd2ebde997fa6ec501bb0fe529a5cabc789#diff-0).

If it still fails at installing v8, install libv8 system-wide and update the bundle dependencies:

`bundle update`

The rest of this section again follows the original:

``` sh
cp config/database.yml.mysql config/database.yml
# or
cp config/database.yml.postgresql config/database.yml
# make sure to update username/password in config/database.yml

# Setup DB
bundle exec rake db:setup RAILS_ENV=production

# Setup schedules 
bundle exec whenever -w RAILS_ENV=production

# Now exit from gitlab_ci user
exit
```

### 6. Init Script

Save yourself the hassle of try-and-error with the linux based script and give [this rc.d script](rc.d-2.2.sh) a go...  
Just copy it into `/usr/local/etc/rc.d/`, name it `gitlab_ci` and make it executable like `chmod +x gitlab_ci`

### 7 & 8 see [original install instructions](https://github.com/gitlabhq/gitlab-ci/blob/2-2-stable/doc/installation.md)


