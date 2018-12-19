# Logstash Patterns

[官方文档](https://www.elastic.co/guide/en/logstash/current/plugins-filters-grok.html)

## Centos7安装GrokDebug
[github地址](https://github.com/nickethier/grokdebug)

```
# 安装依赖
yum install openssl-devel.x86_64 gcc.x86_64 -y

# 下载ruby
wget https://ruby.taobao.org/mirrors/ruby/2.1/ruby-2.1.7.tar.gz

# 安装ruby
tar -zxvf ruby-2.1.7.tar.gz && cd ruby-2.1.7/
./configure --prefix=/usr/local/ruby2.1.7
make && make install

# 配置环境变量
echo 'export PATH=/usr/local/ruby2.1.7/bin:$PATH'>>/etc/profile
source /etc/profile

# 安装rubygem
wget http://rubygems.global.ssl.fastly.net/rubygems/rubygems-2.6.2.tgz
tar -zxvf rubygems-2.6.2.tgz && cd rubygems-2.6.2/
ruby setup.rb

# 替换gem源
gem sources --add https://gems.ruby-china.com/ --remove https://rubygems.org/
gem sources -l

# 创建安装目录与下载grokdebug
mkdir /usr/local/grokdebug && cd /usr/local/grokdebug
git clone https://github.com/nickethier/grokdebug.git
mv grokdebug/* .
rm -rf grokdebug/

# 查看缺少的组件
ruby config.ru

# 安装组件
gem install bundler
gem install cabin -v=0.5.0
gem install haml -v=3.1.7
gem install jls-grok -v=0.10.10
gem install json -v=1.7.5
gem install kgio -v=2.8.0
gem install rack -v=1.4.1
gem install rack-protection -v=1.2.0
gem install raindrops -v=0.11.0  
gem install shotgun -v=0.9
gem install tilt -v=1.3.3
gem install sinatra -v=1.3.3
gem install unicorn -v=4.6.3
```

启动grokdebug

```
nohup bundle exec unicorn -p 8081 -c ./unicorn &
```
