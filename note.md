## com.zaxxer.hikari.HikariConfig.seal when call
- com.zaxxer.hikari.HikariDataSource.getConnection()
- com.zaxxer.hikari.HikariDataSource.HikariDataSource(com.zaxxer.hikari.HikariConfig)


PoolEntry-->Connection
         -->HikariPool  为了recycle 返还给pool
         -->lastAccessed  1.为了淘汰空闲 2.getConnection 判断是否未访问访问的时间间隔超过一定值，需要判断connection是否存活？
         -->lastBorrowed  1.just for stat


ProxyConnection --> Connection
                --> PollEntry
                --> ProxyLeakTask leakTask
                --> FastList<Statement> openStatements
                --> lastAccess

HikariPool      --> ConcurrentBag<PoolEntry> connectionBag



1. Why ProxyConnection contains isReadOnly,isAutoCommit,transactionIsolation, get from delegate connection?
   When ProxyConnection is closed, the delegate will be ClosedConnection.CLOSED_CONNECTION



##  source trail bug问题

mvn help:effective-pom -Doutput="D:/develop/study/bit-vincent/HikariCP/sourcetrail_dependencies/70af8606-8e89-4e2c-becc-afdb8bf22d42/maven/effective-pom.xml"


## ConcurrentBag



### PoolEntry 的状态
- reserve
  not_in_use--> reserved

- unreserve

reserved-->not_in_use

if waiter>0 handoff to a requester.

- add
  add to sharedList

if waiter>0 handoff to a requester.

-remove
reserved-->removed
not_in_user-->removed

remove from sharedList
remove from threadList

- borrow

not_in_use-->in_use

get from threadList

if can't get from threadList,then get from sharedList

if can't get from sharedList, request to add more PoolEntry,  then wait for handoff

--requite

in_use-->not_in_use

if waiter>0,  handoff to a requester

if threadList.size()<50 ,add to threadList


每次commit指向上一次commit，最终形成一个链条，每一条链条就是一个分支。
分支指向一个链条的最后一个commit。  .git\refs\heads
HEAD指向当前分支，即当前分支的最后一个commit。  .git\HEAD 文件
tag指向一个commit。  .git\refs\tags

## 创建tag
git tag xx
## 删除tag
git tag -d xx
## 查看tag
git tag
## 查看tag在哪个commit上
git rev-parse xx
