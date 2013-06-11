<!--
author: JP Richardson
publish: Tue Sep 08 2009 19:39:44 GMT-0500 (CDT)
status: publish
type: post
link: https://procbits.wordpress.com/2009/09/08/sqlite-bulk-insert/
tags: C#
slug: 2009/09/08/sqlite-bulk-insert
title: SQLite Bulk Insert In C#/.NET
-->



Recently, I had a project where I needed to load 1 million+ records into
a SQLite database. I downloaded the [SQLite ADO.NET
adapter](http://sqlite.phxsoftware.com/) and setup the Entity framework
to map to my SQLite database. All was simple and all was well!

I started inserting the data into my database; lo and behold, it was
taking forever! In fact, it took most of a full working day to insert my
data. I knew something wasn't right. A simple Google search pointed me
to the [SQLite FAQ](http://www.sqlite.org/faq.html#q19). It turns out
that SQLite wraps every INSERT into a transaction. Simple solution:
start a transaction and perform multiple INSERTs. I needed my inserts to
be fast, so I just had to write a class to encapsulate this.

It's very simple to use. Make sure that your SQLite database file has
been created and your table has been created as well.

Let's assume your database schema looks like the following: Id INTEGER
PRIMARY KEY AUTOINCREMENT NOT NULL, LastName VARCHAR(16) NOT NULL,
Height REAL NOT NULL

You would then use SQLiteBulkInsert as follows:

```csharp
SQLiteBulkInsert sbi = new SQLiteBulkInsert(yourDatabaseConnectionObject, "yourTableName");
sbi.AddParameter("LastName", DbType.String);
sbi.AddParameter("Height", DbType.Single);
```

You can then insert records:

```csharp
for (int x = 0; x &lt; 10000; x++)
    sbi.Insert(new object[]{someString, someFloat});
sbi.Flush();
```

That's it! You should look at the file SQLiteBulkInsertTest.cs for more
details. SQLiteBulkInsert.cs:

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Data.SQLite;
using System.Data;

namespace YourProject.Data.SQLite
{
    public class SQLiteBulkInsert
    {
        private SQLiteConnection m_dbCon;
        private SQLiteCommand m_cmd;
        private SQLiteTransaction m_trans;

        private Dictionary m_parameters = new Dictionary();

        private uint m_counter = 0;

        private string m_beginInsertText;

        public SQLiteBulkInsert(SQLiteConnection dbConnection, string tableName) {
            m_dbCon = dbConnection;
            m_tableName = tableName;

            StringBuilder query = new StringBuilder(255);
            query.Append("INSERT INTO ["); query.Append(tableName); query.Append("] (");
            m_beginInsertText = query.ToString();
        }

        private bool m_allowBulkInsert = true;
        public bool AllowBulkInsert { get { return m_allowBulkInsert; } set { m_allowBulkInsert = value; } }

        public string CommandText {
            get {
                if (m_parameters.Count &lt; 1)
                    throw new SQLiteException("You must add at least one parameter.");

                StringBuilder sb = new StringBuilder(255);
                sb.Append(m_beginInsertText);

                foreach (string param in m_parameters.Keys) {
                    sb.Append('[');
                    sb.Append(param);
                    sb.Append(']');
                    sb.Append(", ");
                }
                sb.Remove(sb.Length - 2, 2);

                sb.Append(") VALUES (");

                foreach (string param in m_parameters.Keys) {
                    sb.Append(m_paramDelim);
                    sb.Append(param);
                    sb.Append(", ");
                }
                sb.Remove(sb.Length - 2, 2);

                sb.Append(")");

                return sb.ToString();
            }
        }

        private uint m_commitMax = 10000;
        public uint CommitMax { get { return m_commitMax; } set { m_commitMax = value; } }

        private string m_tableName;
        public string TableName { get { return m_tableName; } }

        private string m_paramDelim = ":";
        public string ParamDelimiter { get { return m_paramDelim; } }

        public void AddParameter(string name, DbType dbType) {
            SQLiteParameter param = new SQLiteParameter(m_paramDelim + name, dbType);
            m_parameters.Add(name, param);
        }

        public void Flush() {
            try {
                if (m_trans != null)
                    m_trans.Commit();
            }
            catch (Exception ex) { throw new Exception("Could not commit transaction. See InnerException for more details", ex); }
            finally {
                if (m_trans != null)
                    m_trans.Dispose();

                m_trans = null;
                m_counter = 0;
            }
        }

        public void Insert(object[] paramValues) {
            if (paramValues.Length != m_parameters.Count)
                throw new Exception("The values array count must be equal to the count of the number of parameters.");

            m_counter++;

            if (m_counter == 1) {
                if (m_allowBulkInsert)
                    m_trans = m_dbCon.BeginTransaction();

                m_cmd = m_dbCon.CreateCommand();
                foreach (SQLiteParameter par in m_parameters.Values)
                    m_cmd.Parameters.Add(par);

                m_cmd.CommandText = this.CommandText;
            }

            int i = 0;
            foreach (SQLiteParameter par in m_parameters.Values) {
                par.Value = paramValues[i];
                i++;
            }

            m_cmd.ExecuteNonQuery();

            if (m_counter == m_commitMax) {
                try {
                    if (m_trans != null)
                        m_trans.Commit();
                }
                catch (Exception ex) { }
                finally {
                    if (m_trans != null) {
                        m_trans.Dispose();
                        m_trans = null;
                    }

                    m_counter = 0;
                }
            }
        }
    }
}
```

SQLiteBulkInsertTest.cs:

```csharp
using YourProject.Data.SQLite;
using Microsoft.VisualStudio.TestTools.UnitTesting;
using System.Data.SQLite;
using System.Data;
using System.IO;
using System;

namespace TestYourProject
{
    /// 
    ///This is a test class for SQLiteBulkInsertTest and is intended
    ///to contain all SQLiteBulkInsertTest Unit Tests
    ///
    [TestClass()]
    public class SQLiteBulkInsertTest
    {
        private static string m_testDir;
        private static string m_testFile;
        private static string m_testTableName = "test_table";
        private static string m_connectionString;

        private static string m_deleteAllQuery = "DELETE FROM [{0}]";
        private static string m_countAllQuery = "SELECT COUNT(id) FROM [{0}]";
        private static string m_selectAllQuery = "SELECT * FROM [{0}]";

        private static SQLiteConnection m_dbCon;
        private static SQLiteCommand m_deleteAllCmd;
        private static SQLiteCommand m_countAllCmd;
        private static SQLiteCommand m_selectAllCmd;

        private TestContext testContextInstance;

        /// 
        ///Gets or sets the test context which provides
        ///information about and functionality for the current test run.
        ///
        public TestContext TestContext {
            get {
                return testContextInstance;
            }
            set {
                testContextInstance = value;
            }
        }

        #region Additional test attributes
        //
        //You can use the following additional attributes as you write your tests:
        //
        //Use ClassInitialize to run code before running the first test in the class
        [ClassInitialize()]
        public static void MyClassInitialize(TestContext testContext){
            Random rand = new Random(Environment.TickCount);
            int rn = rand.Next(0, int.MaxValue);
            m_testDir = @"C:\SqliteBulkInsertTest-" + rn + @"\";
            m_testFile = m_testDir + "db.sqlite";

            if (!Directory.Exists(m_testDir))
                Directory.CreateDirectory(m_testDir);

            if (!File.Exists(m_testFile)) {
                FileStream fs = File.Create(m_testFile);
                fs.Close();
            }

            m_connectionString = string.Format(@"data source={0};datetimeformat=Ticks", m_testFile);
            m_dbCon = new SQLiteConnection(m_connectionString);
            m_dbCon.Open();

            SQLiteCommand cmd = m_dbCon.CreateCommand();
            string query = "CREATE TABLE IF NOT EXISTS [{0}] (id INTEGER PRIMARY KEY AUTOINCREMENT, somestring VARCHAR(16), somereal REAL, someint INTEGER(4), somedt DATETIME)";
            query = string.Format(query, m_testTableName);
            cmd.CommandText = query;
            cmd.ExecuteNonQuery();
        }
        //
        //Use ClassCleanup to run code after all tests in a class have run
        [ClassCleanup()]
        public static void MyClassCleanup(){
            m_dbCon.Close();

            File.Delete(m_testFile);
            Directory.Delete(m_testDir);
        }
        #endregion

        private void AddParameters(SQLiteBulkInsert target) {
            target.AddParameter("somestring", DbType.String);
            target.AddParameter("somereal", DbType.String);
            target.AddParameter("someint", DbType.Int32);
            target.AddParameter("somedt", DbType.DateTime);
        }

        private long CountRecords() {
            m_countAllCmd = m_dbCon.CreateCommand();
            m_countAllCmd.CommandText = string.Format(m_countAllQuery, m_testTableName);

            long ret = (long)m_countAllCmd.ExecuteScalar();
            m_countAllCmd.Dispose();

            return ret;
        }

        private void DeleteRecords() {
            m_deleteAllCmd = m_dbCon.CreateCommand();
            m_deleteAllCmd.CommandText = string.Format(m_deleteAllQuery, m_testTableName);

            m_deleteAllCmd.ExecuteNonQuery();
            m_deleteAllCmd.Dispose();
        }

        private SQLiteDataReader SelectAllRecords() {
            m_selectAllCmd = m_dbCon.CreateCommand();
            m_selectAllCmd.CommandText = string.Format(m_selectAllQuery, m_testTableName);
            return m_selectAllCmd.ExecuteReader();
        }

        [TestMethod()]
        public void AddParameterTest() {
            SQLiteBulkInsert target = new SQLiteBulkInsert(m_dbCon, m_testTableName);
            AddParameters(target);

            string pd = target.ParamDelimiter;
            string expectedStmnt = "INSERT INTO [{0}] ([somestring], [somereal], [someint], [somedt]) VALUES ({1}somestring, {2}somereal, {3}someint, {4}somedt)";
            expectedStmnt = string.Format(expectedStmnt, m_testTableName, pd, pd, pd, pd);
            Assert.AreEqual(expectedStmnt, target.CommandText);
        }

        [TestMethod()]
        public void SQLiteBulkInsertConstructorTest() {
            SQLiteBulkInsert target = new SQLiteBulkInsert(m_dbCon, m_testTableName);
            Assert.AreEqual(m_testTableName, target.TableName);

            bool wasException = false;
            try {
                string a = target.CommandText;
            }
            catch (SQLiteException ex) { wasException = true; }

            Assert.IsTrue(wasException);
        }

        [TestMethod()]
        public void CommandTextTest() {
            SQLiteBulkInsert target = new SQLiteBulkInsert(m_dbCon, m_testTableName);
            AddParameters(target);

            string pd = target.ParamDelimiter;
            string expectedStmnt = "INSERT INTO [{0}] ([somestring], [somereal], [someint], [somedt]) VALUES ({1}somestring, {2}somereal, {3}someint, {4}somedt)";
            expectedStmnt = string.Format(expectedStmnt, m_testTableName, pd, pd, pd, pd);
            Assert.AreEqual(expectedStmnt, target.CommandText);
        }

        [TestMethod()]
        public void TableNameTest() {
            SQLiteBulkInsert target = new SQLiteBulkInsert(m_dbCon, m_testTableName);
            Assert.AreEqual(m_testTableName, target.TableName);
        }

        [TestMethod()]
        public void InsertTest() {
            SQLiteBulkInsert target = new SQLiteBulkInsert(m_dbCon, m_testTableName);

            bool didThrow = false;
            try {
                target.Insert(new object[] { "hello" }); //object.length must equal the number of parameters added
            }
            catch (Exception ex) { didThrow = true; }
            Assert.IsTrue(didThrow);

            AddParameters(target);

            target.CommitMax = 4;
            DateTime dt1 = DateTime.Now; DateTime dt2 = DateTime.Now; DateTime dt3 = DateTime.Now; DateTime dt4 = DateTime.Now;
            target.Insert(new object[] { "john", 3.45f, 10, dt1 });
            target.Insert(new object[] { "paul", -0.34f, 100, dt2 });
            target.Insert(new object[] { "ringo", 1000.98f, 1000, dt3 });
            target.Insert(new object[] { "george", 5.0f, 10000, dt4 });

            long count = CountRecords();
            Assert.AreEqual(4, count);

            SQLiteDataReader reader = SelectAllRecords();

            Assert.IsTrue(reader.Read());
            Assert.AreEqual("john", reader.GetString(1)); Assert.AreEqual(3.45f, reader.GetFloat(2));
            Assert.AreEqual(10, reader.GetInt32(3)); Assert.AreEqual(dt1, reader.GetDateTime(4));

            Assert.IsTrue(reader.Read());
            Assert.AreEqual("paul", reader.GetString(1)); Assert.AreEqual(-0.34f, reader.GetFloat(2));
            Assert.AreEqual(100, reader.GetInt32(3)); Assert.AreEqual(dt2, reader.GetDateTime(4));

            Assert.IsTrue(reader.Read());
            Assert.AreEqual("ringo", reader.GetString(1)); Assert.AreEqual(1000.98f, reader.GetFloat(2));
            Assert.AreEqual(1000, reader.GetInt32(3)); Assert.AreEqual(dt3, reader.GetDateTime(4));

            Assert.IsTrue(reader.Read());
            Assert.AreEqual("george", reader.GetString(1)); Assert.AreEqual(5.0f, reader.GetFloat(2));
            Assert.AreEqual(10000, reader.GetInt32(3)); Assert.AreEqual(dt4, reader.GetDateTime(4));

            Assert.IsFalse(reader.Read());

            DeleteRecords();

            count = CountRecords();
            Assert.AreEqual(0, count);
        }

        [TestMethod()]
        public void FlushTest() {
            string[] names = new string[] { "metalica", "beatles", "coldplay", "tiesto", "t-pain", "blink 182", "plain white ts", "staind", "pink floyd" };
            Random rand = new Random(Environment.TickCount);

            SQLiteBulkInsert target = new SQLiteBulkInsert(m_dbCon, m_testTableName);
            AddParameters(target);

            target.CommitMax = 1000;

            //Insert less records than commitmax
            for (int x = 0; x &lt; 50; x++)
                target.Insert(new object[] { names[rand.Next(names.Length)], (float)rand.NextDouble(), rand.Next(50), DateTime.Now });

            //Close connect to verify records were not inserted
            m_dbCon.Close();

            m_dbCon = new SQLiteConnection(m_connectionString);
            m_dbCon.Open();

            long count = CountRecords();
            Assert.AreEqual(0, count);

            //Now actually verify flush worked
            target = new SQLiteBulkInsert(m_dbCon, m_testTableName);
            AddParameters(target);

            target.CommitMax = 1000;

            //Insert less records than commitmax
            for (int x = 0; x &lt; 50; x++)
                target.Insert(new object[] { names[rand.Next(names.Length)], (float)rand.NextDouble(), rand.Next(50), DateTime.Now });

            target.Flush();

            count = CountRecords();
            Assert.AreEqual(50, count);

            //Close connect to verify flush worked
            m_dbCon.Close();

            m_dbCon = new SQLiteConnection(m_connectionString);
            m_dbCon.Open();

            count = CountRecords();
            Assert.AreEqual(50, count);

            DeleteRecords();
            count = CountRecords();
            Assert.AreEqual(0, count);
        }

        [TestMethod()]
        public void CommitMaxTest() {
            SQLiteBulkInsert target = new SQLiteBulkInsert(m_dbCon, m_testTableName);

            target.CommitMax = 4;
            Assert.AreEqual(4, target.CommitMax);

            target.CommitMax = 1000;
            Assert.AreEqual(1000, target.CommitMax);
        }

        //SPEED TEST
        [TestMethod()]
        public void AllowBulkInsertTest() {
            string[] names = new string[] { "metalica", "beatles", "coldplay", "tiesto", "t-pain", "blink 182", "plain white ts", "staind", "pink floyd"};
            Random rand = new Random(Environment.TickCount);

            SQLiteBulkInsert target = new SQLiteBulkInsert(m_dbCon, m_testTableName);
            AddParameters(target);

            const int COUNT = 100;

            target.CommitMax = COUNT;

            DateTime start1 = DateTime.Now;
            for (int x = 0; x &lt; COUNT; x++)
                target.Insert(new object[] { names[rand.Next(names.Length)], (float)rand.NextDouble(), rand.Next(COUNT), DateTime.Now });

            DateTime end1 = DateTime.Now;
            TimeSpan delta1 = end1 - start1;

            DeleteRecords();

            target.AllowBulkInsert = false;
            DateTime start2 = DateTime.Now;
            for (int x = 0; x &lt; COUNT; x++)
                target.Insert(new object[] { names[rand.Next(names.Length)], (float)rand.NextDouble(), rand.Next(COUNT), DateTime.Now });

            DateTime end2 = DateTime.Now;
            TimeSpan delta2 = end2 - start2;

            //THIS MAY FAIL DEPENDING UPON THE MACHINE THE TEST IS RUNNING ON.
            Assert.IsTrue(delta1.TotalSeconds &lt; 0.1); //approx true for 100 recs             Assert.IsTrue(delta2.TotalSeconds &gt; 1.0); //approx true for 100 recs;

            //UNCOMMENT THIS TO MAKE IT FAIL AND SEE ACTUAL NUMBERS IN FAILED REPORT
            //Assert.AreEqual(delta1.TotalSeconds, delta2.TotalSeconds);

            DeleteRecords();
        }
    }
}
```

Enjoy. Drop me a line if you have any problems.

Are you a [Git](http://gitpilot.com) user? Let me help you make project
management with Git simple. Checkout [Gitpilot](http://gitpilot.com).

Read my blog on entrepreneurship: [Techneur](http://techneur.com) Follow
me on Twitter: [@jprichardson](http://twitter.com/jprichardson)

-JP
