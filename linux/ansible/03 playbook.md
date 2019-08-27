playbook是yaml格式的命令集, 通过ansible-playbook命令来执行

```shell
ansible-playbook [options] playbook.yml [playbook2 ...]
-C --check 不实际运行
-f FORKS 用来做并发的
--list-hosts 列出主机列表
--syntax-check 语法检查
-e 传递参数
-t 指定tags
```

#### playbook文件

格式

```yaml
- hosts: 主机
  remote_user: 执行用户
  tasks:
  - name: 任务名
    模块名: 参数
  - name: 任务名
    模块名: 参数
```

任务顺序执行, 多台机器时,等所有机器都执行完第一个任务时, 再去执行第二个任务

#### playbook的参数

使用{{}}将变量从扩起,  可以从外部传递这个变量. 不用每次都修改文件

```ymal
- hosts: web
  remote_user: root
  tasks:
  -  name: create{{ user }}
     user: name={{ user }}
```

第一种传参方式, 使用-e

```shell
ansible-playbook -e user=wo,file=a   a.yml # 多个变量用,隔开
```

第二种方式: hosts文件里面写在主机后面

```yaml
[web]
10.0.0.152 user=wo,file=a
```

第三种方式: hosts文件里面写[groupname:vars]

```
[web]
10.0.0.152
[web:vars]
user=wo
file=a
```

第四种方式: playbook文件中vars来指定

```yaml
- hosts: web
  remote_user: root
  vars:
  - user: alexsb5
  tasks:
  -  name: create{{ user }}
     user: name={{ user }}
```

第五种传参方式:通过register注册,使用的时候要使用参数的.stdout值

```yaml
- hosts: web
  remote_user: root
  tasks:
  -  name: sum
     shell: echo 2+4|bc
     register: user 
  -  name: create{{ user }}
     user: name=alexsb{{ user.stdout }}
```

优先级

```shell
-e > playbooks的vars > hosts文件
```

#### 条件判断

```yaml
- hosts: cache
  tasks:
  - name: createfile
    copy: content="小弦切切如私语" dest=/tmp/ppx.txt
    when: ansible_processor_vcpus==1 # 这个key可以是外面传进来的, 也可以是收集的信息, 不用{{}}
```

#### 标签

将任务设置标签, 可以执行剧本中指定的标签

```yaml
- hosts: cache
  remote_user: root
  tasks:
  - name: install
    yum: name=redis
    tags: install
  - name: copyfile
    copy: dest=/etc/redis.conf src=/root/yaml/redis.conf
    tags: copyfile
  - name: startredis
    service: name=redis state=started
    tags: start
```

执行

```shell
ansible-playbook -t start   a.yml
```

#### 循环

with_items是集合,item代表着其中的一个变量, 会将每个变量都执行一次, 如下就会创建3个用户

```yaml
- hosts: db
  tasks:
  - name: creatuser
    user: name={{ item }}
    with_items:
    - alex10
    - wusir10
    - taibai10
```

with_items还支持对象的写法

```yaml
- hosts: db
  tasks:
  - name: creatuser
    user: name={{ item.name }} group={{item.group}}
    with_items:
    - {name: alex30,group: alex20}
    - {name: wusir30,group: wusir20}
    - {name: taibai30,group: taibai20}
```

#### handlers

handlers与tag相似, 都是指定执行的, 不过tag是在命令行指定, 而handlers是在剧本中指定

```yaml
- hosts: cache
  remote_user: root
  tasks:
  - name: install
    yum: name=redis
    tags: install
  - name: copyfile
    template: dest=/etc/redis.conf src=redis.conf.j2
    tags: copyfile
    notify: restart redis # notify: handlers名会在这个任务执行完后执行这个handlers
  - name: startredis
    service: name=redis state=started
    tags: start
  handlers:
  - name: restart redis
    service: name=redis state=restarted # 也是模块名: 参数
```

