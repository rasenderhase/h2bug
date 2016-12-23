# h2bug
Demonstration of "unexpected code path" in H2 Session.addLock() method

see also [https://groups.google.com/forum/#!topic/h2-database/x2XoFP1G2l8]
and [http://stackoverflow.com/questions/41293469/h2-database-in-tomcat-environment-throwing-runtimeexception-unexpected-code-pat].

## Eclipse project
Check out the project and import it to eclipse.

Create a tomcat runtime environment and set the configuration path to `h2bug/tomcat_configuration`. Deploy the application.

Then connect to / create the database using your favorite SQL JDBC tool. The URL is `jdbc:h2:~/h2bug;AUTO_SERVER=TRUE`. Run the creation script `h2bug/sql/create.sql`. Disconnect.

Start tomcat and surf to [http://localhost:8080/h2bug/TestServlet]. The site should ask for user name and password. Enter `ben` in both fields.

Watch the console log:
```
2016-12-23 20:11:06 jdbc[3]: 
/**/conn3.setAutoCommit(false);
2016-12-23 20:11:06 jdbc[3]: 
/**/PreparedStatement prep8 = conn3.prepareStatement("select aufgabenliste_seq.nextval");
2016-12-23 20:11:06 jdbc[3]: Plan       : calculate cost for plan [SYSTEM_RANGE:0:org.h2.table.RangeTable@4b600d87]
2016-12-23 20:11:06 jdbc[3]: Plan       :   for table filter SYSTEM_RANGE:0:org.h2.table.RangeTable@4b600d87
2016-12-23 20:11:06 jdbc[3]: Table      :     potential plan item cost 1 index PUBLIC.RANGE_INDEX
2016-12-23 20:11:06 jdbc[3]: Plan       :   best plan item cost 1 index PUBLIC.RANGE_INDEX
2016-12-23 20:11:06 jdbc[3]: Plan       : plan cost 2
2016-12-23 20:11:06 jdbc[3]: 
/**/ResultSet rs9 = prep8.executeQuery();
2016-12-23 20:11:06 lock: 1 exclusive write lock requesting for SYS
2016-12-23 20:11:06 lock: 1 exclusive write lock added for SYS
2016-12-23 20:11:06 lock: 1 exclusive write lock unlock SYS
2016-12-23 20:11:06 lock: 1 exclusive write lock requesting for SYS
2016-12-23 20:11:06 lock: 1 exclusive write lock added for SYS
2016-12-23 20:11:06 jdbc[3]: exception
org.h2.jdbc.JdbcSQLException: Allgemeiner Fehler: "java.lang.RuntimeException: Unexpected code path"
General error: "java.lang.RuntimeException: Unexpected code path"; SQL statement:
select aufgabenliste_seq.nextval [50000-193]
	at org.h2.message.DbException.getJdbcSQLException(DbException.java:345)
	at org.h2.message.DbException.get(DbException.java:168)
	at org.h2.message.DbException.convert(DbException.java:295)
	at org.h2.command.Command.executeQuery(Command.java:213)
	at org.h2.jdbc.JdbcPreparedStatement.executeQuery(JdbcPreparedStatement.java:110)
	at org.apache.tomcat.dbcp.dbcp2.DelegatingPreparedStatement.executeQuery(DelegatingPreparedStatement.java:82)
	at org.apache.tomcat.dbcp.dbcp2.DelegatingPreparedStatement.executeQuery(DelegatingPreparedStatement.java:82)
	at h2bug.TestServlet.doGet(TestServlet.java:41)
	at javax.servlet.http.HttpServlet.service(HttpServlet.java:622)
	at javax.servlet.http.HttpServlet.service(HttpServlet.java:729)
	at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:230)
	at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:165)
	at org.apache.tomcat.websocket.server.WsFilter.doFilter(WsFilter.java:53)
	at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:192)
	at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:165)
	at org.apache.catalina.core.StandardWrapperValve.invoke(StandardWrapperValve.java:199)
	at org.apache.catalina.core.StandardContextValve.invoke(StandardContextValve.java:108)
	at org.apache.catalina.authenticator.AuthenticatorBase.invoke(AuthenticatorBase.java:586)
	at org.apache.catalina.core.StandardHostValve.invoke(StandardHostValve.java:140)
	at org.apache.catalina.valves.ErrorReportValve.invoke(ErrorReportValve.java:79)
	at org.apache.catalina.valves.AbstractAccessLogValve.invoke(AbstractAccessLogValve.java:620)
	at org.apache.catalina.core.StandardEngineValve.invoke(StandardEngineValve.java:87)
	at org.apache.catalina.connector.CoyoteAdapter.service(CoyoteAdapter.java:349)
	at org.apache.coyote.http11.Http11Processor.service(Http11Processor.java:784)
	at org.apache.coyote.AbstractProcessorLight.process(AbstractProcessorLight.java:66)
	at org.apache.coyote.AbstractProtocol$ConnectionHandler.process(AbstractProtocol.java:802)
	at org.apache.tomcat.util.net.NioEndpoint$SocketProcessor.doRun(NioEndpoint.java:1452)
	at org.apache.tomcat.util.net.SocketProcessorBase.run(SocketProcessorBase.java:49)
	at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1142)
	at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:617)
	at org.apache.tomcat.util.threads.TaskThread$WrappingRunnable.run(TaskThread.java:61)
	at java.lang.Thread.run(Thread.java:744)
Caused by: java.lang.RuntimeException: Unexpected code path
	at org.h2.message.DbException.throwInternalError(DbException.java:242)
	at org.h2.message.DbException.throwInternalError(DbException.java:255)
	at org.h2.engine.Session.addLock(Session.java:842)
	at org.h2.mvstore.db.MVTable.doLock2(MVTable.java:254)
	at org.h2.mvstore.db.MVTable.doLock1(MVTable.java:202)
	at org.h2.mvstore.db.MVTable.lock(MVTable.java:167)
	at org.h2.engine.Database.lockMeta(Database.java:909)
	at org.h2.engine.Database.removeSchemaObject(Database.java:1842)
	at org.h2.table.Table.removeChildrenAndResources(Table.java:530)
	at org.h2.table.TableView.removeChildrenAndResources(TableView.java:414)
	at org.h2.engine.Session.cleanTempTables(Session.java:948)
	at org.h2.engine.Session.commit(Session.java:643)
	at org.h2.schema.Sequence.flush(Sequence.java:302)
	at org.h2.schema.Sequence.getNext(Sequence.java:269)
	at org.h2.expression.SequenceValue.getValue(SequenceValue.java:30)
	at org.h2.command.dml.Select.queryFlat(Select.java:549)
	at org.h2.command.dml.Select.queryWithoutCache(Select.java:655)
	at org.h2.command.dml.Query.query(Query.java:341)
	at org.h2.command.dml.Query.query(Query.java:309)
	at org.h2.command.dml.Query.query(Query.java:36)
	at org.h2.command.CommandContainer.query(CommandContainer.java:110)
	at org.h2.command.Command.executeQuery(Command.java:201)
	... 28 more
2016-12-23 20:11:06 jdbc[3]: 
/**/prep8.close();
