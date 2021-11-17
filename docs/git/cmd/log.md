# log

git log 查看git提交历史

1、`git log` 将会展示

```
commit c0749c8ff4eb9287f69fecaeb7105cb1b4f59d10
Author: richLpf <lpengfei66@163.com>
Date:   Thu Jul 16 10:05:01 2020 +0800

    task

commit efd9b2347c8a67cf49b865845a216f13edcc6faa
Author: richLpf <lpengfei66@163.com>
Date:   Thu Jul 16 10:03:41 2020 +0800

    test

```
我们可以看到commitid, Author, Date和提交的注释信息

2、git log -[num]

示例：`git log -2`

展示两条提交日志

3、`git log -p` 或 `git log --patch`

展示提交的文件修改的详细信息

4、`git log --stat`

5、`git log --oneline` 

6、`git log --after="2020-15-05"`

7、`git log --after="2020-15-05" --before="2020-25-05"`

`git log --since=2.weeks`  `--until` == `--before`

时间还可以是：yesterday, today, 10 day ago, 1 week ago, 2 month ago

8、git log --author="pengfei"

9、过滤`git log --grep="test"`

10、忽略大消息查询 `git log -i --grep="test"`

11、过滤多个参数，查询参数中间使用`\|` 连接 `git log --grep="test\|测试"`

12、查询单个文件提交记录 `git log index.html`  多个文件 `git log index.html index.js`

13、`git log -S"function compare"` 查找文件中的某段代码添加的提交记录

14、`git log --merges` 查看当前分支合并的记录

15、`git log master..dev` 

16、定义打印日志格式 

> `git log --pretty=format:"%Cred%an - %ar%n %Cblue %h -%Cgreen %s %n"`
> `git log --pretty=oneline`

选项 说明
%H 提交的完整哈希值
%h 提交的简写哈希值
%T 树的完整哈希值
%t 树的简写哈希值
%P 父提交的完整哈希值
%p 父提交的简写哈希值
%an 作者名字
%ae 作者的电子邮件地址
%ad 作者修订日期（可以用 --date=选项 来定制格式）
%ar 作者修订日期，按多久以前的方式显示
%cn 提交者的名字
%ce 提交者的电子邮件地址
%cd 提交日期
%cr 提交日期（距今多长时间）
%s 提交说明

可以使用red等颜色

17、`git log --graph` 图形化展示各分支的提交历史

18、`git log --no-merges` 仅展示提交的版本，不展示合并分支的提交

注意：这些命令可以相互组合，下面通过几个例子来灵活的使用这些命令

1、仅展示两条数据

2、查看index.html文件的提交记录，并查询修改