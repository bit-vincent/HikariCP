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
