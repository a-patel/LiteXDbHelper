# LiteXDbHelper
> LiteXDbHelper is simple and tiny yet powerful and very high-performance library to working with ADO.NET for different database providers in C#.
This library contains useful database utilitity classes, functions and extension methods.
ADO.NET wrapper specifically develop to help make life easy working with relational databases like SQLServer, MySql, PostgreSql, MariaDB, Oracle and stored procedures for .NET and .NET Core applications.
This is a tiny library helps write less code, to execute queries and stored procedures against SQL Server as like any normal CLR method.
It is just written for the purpose to bring a new level of ease to the developers who deal with ADO.NET for data access.


## Database Providers :books:
- [SqlServer](docs/SqlServer.md)
- [MySql](docs/MySql.md)
- [PostgreSql](docs/PostgreSql.md)
- [MariaDB](docs/MariaDB.md)
- [Oracle](docs/Oracle.md)


## Features :pager:
#### Basic features
- ExecuteNonQuery
- ExecuteScalar
- GetDataTable
- GetDataSet
- ExecuteReder
- CreateParameters (for each providers)

#### Advanced features
- ExecuteScalar<>
- GetSingle<T>
- GetList<T>
- GetArray<T>
- GetDictionary<TKey, TValue>


### Instantiate the DBHelper:

```cs
using DBHelpers;
...
// just use the connection string name
IDBHelper dbHelper = new SqlHelper("MyCN");
// use different provider class based on your database.
```

### Basic ADO.NET methods:
```cs
// Create parameters
var paramResultOut = DbHelper.CreateOutParameter("@Result", SqlDbType.NVarChar, 100);
var paramIsActive = DbHelper.CreateParameter("@IsActive", isActive);
var paramId = DbHelper.CreateParameter("@Id", id);

  
// ExecuteNonQuery
// Stored procedure
var count = _dbHelper.ExecuteNonQuery("DeleteCountry", paramIsActive, paramResultOut);
// OR
// Raw sql commnad
var count = _dbHelper.ExecuteNonQuery("DELETE FROM [dbo].[Country] WHERE Id = @Id", CommandType.Text, paramIsActive);


// ExecuteScalar - returning a object type scalar value 
var name = _dbHelper.ExecuteScalar("SELECT Name FROM COUNTRY");


// DataReader
// Stored procedure
var dataReader = _dbHelper.ExecuteReder("GetCountries", paramIsActive, paramResultOut);
// OR
// Raw sql commnad
var dataReader = _dbHelper.ExecuteReder("SELECT * FROM Country WHERE IsActive = @IsActive", CommandType.Text, paramIsActive);


// DataTable
// Stored procedure
var dataTable = _dbHelper.GetDataTable("GetCountries", paramIsActive, paramResultOut);
// OR
// Raw sql commnad
var dataTable = _dbHelper.GetDataTable("SELECT * FROM Country WHERE IsActive = @IsActive", CommandType.Text, paramIsActive);


// DataSet
// Stored procedure
var dataSet = _dbHelper.GetDataSet("GetCountriesAndStates", paramIsActive, paramResultOut);
```

### Scalar methods: (Coming soon)
```cs
// returning a object type scalar value 
var name = _dbHelper.ExecuteScalar("SELECT Name FROM COUNTRY");

// returning a int scalar value
var count = _dbHelper.ExecuteScalar<int>("SELECT COUNT(*) FROM COUNTRY", cmdType: CommandType.Text);

// returning a datetime scalar value
//var lastDate = _dbHelper.ExecuteScalar<DateTime>("SELECT MAX(change_date) FROM TABLENAME");
var valueDatetime = _dbHelper.ExecuteScalar<DateTime>("SELECT GETDATE()", cmdType: CommandType.Text);


// This provides a quite safe experience for most cases and allow you to not care about DBNulls or conversions.

// int is a struct, so value = default(int) = 0
var valueInt = _dbHelper.ExecuteScalar<int>("SELECT CAST(NULL AS int)", cmdType: CommandType.Text);

// string is a reference type, so value = default(string) = null
var valueString = _dbHelper.ExecuteScalar<string>("SELECT CAST(NULL AS varchar)", cmdType: CommandType.Text);

// int? is a nullable, so value = default(int?) = null
var valueNullableInt = _dbHelper.ExecuteScalar<int?>("SELECT CAST(NULL AS int)", cmdType: CommandType.Text);

// value comes as int or long depending on the provider, but is converted to byte using System.Convert
//var valueByte = _dbHelper.ExecuteScalar<byte>("SELECT COUNT(*) FROM TABLENAME", cmdType: CommandType.Text);
```

### Advanced feature (Coming soon)
ADO.NET is not hard to use, but as any low level component it requires a lot of plumbing. It requires you to explicitly open connections and remember to close them. It requires you to convert values and handle DBNulls. As you work with it, it becomes clear that many things could be automated. This library is basically a lot of overloads that do most of this plumbing and let you concentrate on what you need to do. It returns storng type values.

Once instantiated, DBHelper supports executing queries directly to the database and returning useful types in a single command. For example:

```cs
// returning a int scalar value
var count = _dbHelper.ExecuteScalar<int>("SELECT COUNT(*) FROM COUNTRY", cmdType: CommandType.Text);


// get int array 
int[] arrayInt = _dbHelper.GetArray<int>("SELECT Id FROM COUNTRY", cmdType: CommandType.Text);

// get string array 
string[] arrayString = _dbHelper.GetArray<string>("SELECT Name FROM COUNTRY", cmdType: CommandType.Text);


// get key-pair value result in Dictionary
Dictionary<int, string>  dictNameAndId = _dbHelper.GetDictionary<int, string>("SELECT Id, Name FROM COUNTRY", cmdType: CommandType.Text);


// get strong type object
// Stored procedure
var country = _dbHelper.GetSingle<Country>("GetCountryById", paramId, paramResultOut);
// OR
// Raw sql commnad
var country = _dbHelper.GetSingle<Country>("SELECT * FROM Country WHERE Id = @Id", CommandType.Text, paramId);


// get strong type list
// Stored procedure
var countries = _dbHelper.GetList<Country>("GetCountries", paramId, paramResultOut);
// OR
// Raw sql commnad
var countries = _dbHelper.GetList<Country>("SELECT * FROM Country WHERE IsActive = @IsActive", CommandType.Text, paramIsActive);
```

#### Automatic Type Conversion (Coming soon)
---
When loading data from the database, values can be null/DBNull or can be of a slightly different type. DBHelpers adds some extension methods to DbDataReader, so you can safely expect certain types.

This is how you can read data from a table to a list of anonymous objects for quick use:

```cs
var list = _dbHelper.GetList("SELECT Id, Name, NumericIsoCode FROM Country", cmdType: CommandType.Text, r => new {
    ID = r.Get<int>("Id"),
    Name = r.Get<string>("Name"),
    IsoCode = r.Get<int?>("NumericIsoCode")
});
```


## Basic Usage :page_facing_up:

### Step 1 : Install the package :package:

> Choose one kinds of sms provider type that you needs and install it via [Nuget](https://www.nuget.org/profiles/iamaashishpatel).
> To install LiteXDbHelper, run the following command in the [Package Manager Console](http://docs.nuget.org/docs/start-here/using-the-package-manager-console)

```Powershell
PM> Install-Package LiteX.DbHelper.SqlServer
PM> Install-Package LiteX.DbHelper.MySql
PM> Install-Package LiteX.DbHelper.PostgreSql
PM> Install-Package LiteX.DbHelper.MariaDB
PM> Install-Package LiteX.DbHelper.Oracle
```


### Step 2 : Configuration 🔨 
> Different types of database provider have their own way to config.
> Here are samples that show you how to config.

##### 2.1 : AppSettings 
```js
{
  "ConnectionStrings": {
    "LiteXConnection": "Server=AASHISH-PC;Database=LiteXDB;user id=sa;password=PASSWORD;Trusted_Connection=True MultipleActiveResultSets=true;"
}
}
```

##### 2.2 : Configure Startup Class
```cs
// No configuration required
```

### Step 3 : Use in Controller or Business layer :memo:

```cs
 /// <summary>
/// Country controller
/// </summary>
[Route("api/[controller]")]
public class CountryController : Controller
{
    #region Fields

    public readonly string _connectionString;
    private readonly IDbHelper _dbHelper;
    private readonly DbProvider _dbProvider;

    #endregion

    #region Ctor

    /// <summary>
    /// Ctor
    /// </summary>
    public CountryController(IConfiguration configuration)
    {
        _dbProvider = configuration.GetValue<DbProvider>("DbProvider");

        switch (_dbProvider)
        {
            case DbProvider.SqlServer:
                _connectionString = configuration.GetConnectionString("SqlServerConnection");
                _dbHelper = new SqlHelper(_connectionString);
                break;
            //case DbProvider.MySql:
            //    _connectionString = configuration.GetConnectionString("MySqlConnection");
            //    _dbHelper = new LiteX.DbHelper.MySql.MySqlHelper(_connectionString);
            //    break;
            //case DbProvider.NpgSql:
            //    _connectionString = configuration.GetConnectionString("NpgSqlConnection");
            //    _dbHelper = new NpgsqlHelper(_connectionString);
            //    break;
            //case DbProvider.MariaDB:
            //    _connectionString = configuration.GetConnectionString("MariaDBConnection");
            //    _dbHelper = new MariaDBHelper(_connectionString);
            //    break;
            //case DbProvider.Oracle:
            //    _connectionString = configuration.GetConnectionString("OracleConnection");
            //    _dbHelper = new OracleHelper(_connectionString);
            //    break;
            case DbProvider.OleDb:
            case DbProvider.SQLite:
            default:
                _connectionString = configuration.GetConnectionString("SqlServerConnection");
                _dbHelper = new SqlHelper(_connectionString);
                break;
        }
    }

    #endregion

    #region Methods

    /// <summary>
    /// COMING SOON
    /// Get all country list
    /// Strongly type result (type safe)
    /// </summary>
    /// <param name="isActive"></param>
    /// <param name="useStoredProc">Use stored procedure</param>
    /// <returns></returns>
    [HttpGet]
    [Route("get-all")]
    public IActionResult GetAll(bool? isActive, bool useStoredProc = true)
    {
        var paramResultOut = DbHelper.CreateOutParameter("@Result", SqlDbType.NVarChar, 100);
        var paramIsActive = DbHelper.CreateParameter("@IsActive", isActive);
        //var paramResultOut = LiteX.DbHelper.MySql.DbHelper.CreateOutParameter("@Result", MySqlDbType.VarChar, 100);
        //var paramIsActive = LiteX.DbHelper.MySql.DbHelper.CreateParameter("@pIsActive", isActive);
        //var paramResultOut = LiteX.DbHelper.Npgsql.DbHelper.CreateOutParameter("@Result", NpgsqlDbType.Varchar, 100);
        //var paramIsActive = LiteX.DbHelper.Npgsql.DbHelper.CreateParameter("@pIsActive", isActive);

        var countries = new List<Country>();
        if (useStoredProc)
        {
            // Stored procedure
            var spName = "GetCountries";
            countries = _dbHelper.GetList<Country>(spName, paramIsActive, paramResultOut);
        }
        else
        {
            //// WAY - 2
            // Raw sql commnad
            var cmdText = "SELECT * FROM Country WHERE IsActive = @IsActive";
            countries = _dbHelper.GetList<Country>(cmdText, CommandType.Text, paramIsActive);
        }

        // NOT WORKING - Get out parameter value
        var resultOut = paramResultOut.Value;

        if (countries == null || countries.Count == 0)
            return NotFound("No record found");

        return Ok(countries);
    }

    /// <summary>
    /// Get country by id
    /// Strongly type result (type safe)
    /// </summary>
    /// <param name="id"></param>
    /// <param name="useStoredProc">Use stored procedure</param>
    /// <returns></returns>
    [HttpGet]
    [Route("get-by-id/{id}")]
    public IActionResult GetById(int id, bool useStoredProc = true)
    {
        var paramResultOut = DbHelper.CreateOutParameter("@Result", SqlDbType.NVarChar, 100);
        var paramId = DbHelper.CreateParameter("@Id", id);
        //var paramResultOut = LiteX.DbHelper.MySql.DbHelper.CreateOutParameter("@Result", MySqlDbType.VarChar, 100);
        //var paramId = LiteX.DbHelper.MySql.DbHelper.CreateParameter("@pId", id);
        //var paramResultOut = LiteX.DbHelper.Npgsql.DbHelper.CreateOutParameter("@Result", NpgsqlDbType.Varchar, 100);
        //var paramId = LiteX.DbHelper.Npgsql.DbHelper.CreateParameter("@pId", id);

        var country = new Country();
        if (useStoredProc)
        {
            // Stored procedure
            var spName = "GetCountryById";
            country = _dbHelper.GetSingle<Country>(spName, paramId, paramResultOut);

            // using converter
            //country = _dbHelper.GetSingle<Country>(spName, r => r.ParseCountry(), paramId, paramResultOut);
        }
        else
        {
            //WAY - 2
            // Raw sql commnad
            var cmdText = "SELECT * FROM Country WHERE Id = @Id";
            country = _dbHelper.GetSingle<Country>(cmdText, CommandType.Text, paramId);
        }

        // NOT WORKING - Get out parameter value
        var resultOut = paramResultOut.Value;

        if (country == null)
            return NotFound("No record found");

        return Ok(country);
    }

    /// <summary>
    /// Add new country
    /// </summary>
    /// <param name="model"></param>
    /// <param name="useStoredProc"></param>
    /// <returns></returns>
    [HttpPost]
    [Route("add")]
    public IActionResult Add(Country model, bool useStoredProc = true)
    {
        var paramResultOut = DbHelper.CreateOutParameter("@Result", SqlDbType.NVarChar, 100);
        //var paramResultOut = LiteX.DbHelper.MySql.DbHelper.CreateOutParameter("@Result", MySqlDbType.VarChar, 100);
        //var paramResultOut = LiteX.DbHelper.Npgsql.DbHelper.CreateOutParameter("@Result", NpgsqlDbType.Varchar, 100);

        var dbParams = new DbParameter[]
        {
                DbHelper.CreateParameter("Name", model.Name),
                DbHelper.CreateParameter("TwoLetterIsoCode", model.TwoLetterIsoCode),
                DbHelper.CreateParameter("ThreeLetterIsoCode", model.ThreeLetterIsoCode),
                DbHelper.CreateParameter("NumericIsoCode", model.NumericIsoCode),
                DbHelper.CreateParameter("IsActive", model.IsActive),
                DbHelper.CreateParameter("DisplayOrder", model.DisplayOrder),
                paramResultOut
        };

        var result = -1;
        if (useStoredProc)
        {
            // Stored procedure
            var spName = "AddCountry";
            result = _dbHelper.ExecuteScalar<int>(spName, dbParams);

            // get object type result
            //var resultObj = _dbHelper.ExecuteScalar(spName, dbParams);
        }
        else
        {
            //WAY - 2
            // Raw sql commnad
            var cmdText = @"INSERT INTO [dbo].[Country]
	                          (
		                          [Name],
		                          [TwoLetterIsoCode],
		                          [ThreeLetterIsoCode],
		                          [NumericIsoCode],
		                          [IsActive],
		                          [DisplayOrder]
	                          )
	                          VALUES
	                          (
		                          @Name,
		                          @TwoLetterIsoCode,
		                          @ThreeLetterIsoCode,
		                          @NumericIsoCode,
		                          @IsActive,
		                          @DisplayOrder
	                          )";
            result = _dbHelper.ExecuteScalar<int>(cmdText, CommandType.Text, dbParams);
        }

        // NOT WORKING - Get out parameter value
        var resultOut = paramResultOut.Value;

        return Ok(result);
    }

    /// <summary>
    /// Update country details
    /// </summary>
    /// <param name="model"></param>
    /// <param name="useStoredProc"></param>
    /// <returns></returns>
    [HttpPut]
    [Route("update")]
    public IActionResult Update(Country model, bool useStoredProc = true)
    {
        var paramResultOut = DbHelper.CreateOutParameter("@Result", SqlDbType.NVarChar, 100);
        //var paramResultOut = LiteX.DbHelper.MySql.DbHelper.CreateOutParameter("@Result", MySqlDbType.VarChar, 100);
        //var paramResultOut = LiteX.DbHelper.Npgsql.DbHelper.CreateOutParameter("@Result", NpgsqlDbType.Varchar, 100);

        var dbParams = new DbParameter[]
        {
                DbHelper.CreateParameter("Id", model.Id),
                DbHelper.CreateParameter("Name", model.Name),
                DbHelper.CreateParameter("TwoLetterIsoCode", model.TwoLetterIsoCode),
                DbHelper.CreateParameter("ThreeLetterIsoCode", model.ThreeLetterIsoCode),
                DbHelper.CreateParameter("NumericIsoCode", model.NumericIsoCode),
                DbHelper.CreateParameter("IsActive", model.IsActive),
                DbHelper.CreateParameter("DisplayOrder", model.DisplayOrder),
                paramResultOut
        };

        var result = -1;
        if (useStoredProc)
        {
            // Stored procedure
            var spName = "UpdateCountry";
            result = _dbHelper.ExecuteScalar<int>(spName, dbParams);

            // get object type result
            //var resultObj = _dbHelper.ExecuteScalar(spName, dbParams);
        }
        else
        {
            //WAY - 2
            // Raw sql commnad
            var cmdText = @"UPDATE [dbo].[Country]
	                            SET
		                            Name = @Name,
		                            TwoLetterIsoCode = @TwoLetterIsoCode,
		                            ThreeLetterIsoCode = @ThreeLetterIsoCode,
		                            NumericIsoCode = @NumericIsoCode,
		                            IsActive = @IsActive,
		                            DisplayOrder = @DisplayOrder
	                            WHERE Id = @Id";
            result = _dbHelper.ExecuteScalar<int>(cmdText, CommandType.Text, dbParams);
        }

        // NOT WORKING - Get out parameter value
        var resultOut = paramResultOut.Value;

        return Ok(result);
    }

    /// <summary>
    /// Delete country
    /// </summary>
    /// <param name="id"></param>
    /// <param name="useStoredProc"></param>
    /// <returns></returns>
    [HttpDelete]
    [Route("remove/{id}")]
    public IActionResult Remove(int id, bool useStoredProc = true)
    {
        var paramResultOut = DbHelper.CreateOutParameter("@Result", SqlDbType.NVarChar, 100);
        var paramId = DbHelper.CreateParameter("@Id", id);
        //var paramResultOut = LiteX.DbHelper.MySql.DbHelper.CreateOutParameter("@Result", MySqlDbType.VarChar, 100);
        //var paramId = LiteX.DbHelper.MySql.DbHelper.CreateParameter("@pId", id);
        //var paramResultOut = LiteX.DbHelper.Npgsql.DbHelper.CreateOutParameter("@Result", NpgsqlDbType.Varchar, 100);
        //var paramId = LiteX.DbHelper.Npgsql.DbHelper.CreateParameter("@pId", id);

        var resultCount = -1;

        if (useStoredProc)
        {
            // Stored procedure
            var spName = "DeleteCountry";
            resultCount = _dbHelper.ExecuteNonQuery(spName, paramId, paramResultOut);
        }
        else
        {
            //WAY - 2
            // Raw sql commnad
            var cmdText = "DELETE FROM [dbo].[Country] WHERE Id = @Id";
            resultCount = _dbHelper.ExecuteNonQuery(cmdText, CommandType.Text, paramId);
        }

        // NOT WORKING - Get out parameter value
        var resultOut = paramResultOut.Value;

        if (resultCount > 0)
            return Ok(resultCount);

        return StatusCode(500, "Error");
    }

    /// <summary>
    /// Get different type of scalar values 
    /// e.g. int, string, datetime, int?, datetime?
    /// </summary>
    /// <returns></returns>
    [HttpGet]
    [Route("demo-single-values-stats-using-executescalar")]
    public IActionResult DemoExecuteScalar()
    {
        // returning a Scalar value
        var count = _dbHelper.ExecuteScalar<int>("SELECT COUNT(*) FROM COUNTRY", cmdType: CommandType.Text);

        //var lastDate = _dbHelper.ExecuteScalar<DateTime>("SELECT MAX(change_date) FROM TABLENAME");
        var valueDatetime = _dbHelper.ExecuteScalar<DateTime>("SELECT GETDATE()", cmdType: CommandType.Text);


        // This provides a quite safe experience for most cases and allow you to not care about DBNulls or conversions.

        // int is a struct, so value = default(int) = 0
        var valueInt = _dbHelper.ExecuteScalar<int>("SELECT CAST(NULL AS int)", cmdType: CommandType.Text);

        // string is a reference type, so value = default(string) = null
        var valueString = _dbHelper.ExecuteScalar<string>("SELECT CAST(NULL AS varchar)", cmdType: CommandType.Text);

        // int? is a nullable, so value = default(int?) = null
        var valueNullableInt = _dbHelper.ExecuteScalar<int?>("SELECT CAST(NULL AS int)", cmdType: CommandType.Text);

        // value comes as int or long depending on the provider, but is converted to byte using System.Convert
        //var valueByte = _dbHelper.ExecuteScalar<byte>("SELECT COUNT(*) FROM TABLENAME", cmdType: CommandType.Text);

        return Ok(new { CountryCount = count, dateValue = valueDatetime, intValue = valueInt, stringValue = valueString, nullableIntValue = valueNullableInt });
    }

    /// <summary>
    /// Get country name list (array)
    /// and get id list (array)
    /// Return array of single type (e.g. int, string, decimal, char)
    /// </summary>
    /// <returns></returns>
    [HttpGet]
    [Route("get-names")]
    public IActionResult GetNames()
    {
        var arrayInt = _dbHelper.GetArray<int>("SELECT Id FROM COUNTRY", cmdType: CommandType.Text);

        var arrayString = _dbHelper.GetArray<string>("SELECT Name FROM COUNTRY", cmdType: CommandType.Text);

        return Ok(new { Names = arrayString, Ids = arrayInt });
    }

    /// <summary>
    /// Get Name and Id - Key-Value pair (Dictionary) collection
    /// </summary>
    /// <param name="useStoredProc"></param>
    /// <returns></returns>
    [HttpGet]
    [Route("get-name-and-id")]
    public IActionResult GetNameAndId(bool useStoredProc = true)
    {
        var dictNameAndId = new Dictionary<int, string>();

        if (useStoredProc)
        {
            // Stored procedure
            dictNameAndId = _dbHelper.GetDictionary<int, string>("GetCountryNameAndId", cmdType: CommandType.Text);
        }
        else
        {
            dictNameAndId = _dbHelper.GetDictionary<int, string>("SELECT Id, Name FROM COUNTRY", cmdType: CommandType.Text);
        }

        return Ok(dictNameAndId);
    }

    /// <summary>
    /// Get all country and state list (using DataSet)
    /// </summary>
    /// <param name="isActive"></param>
    /// <returns></returns>
    [HttpGet]
    [Route("get-country-and-states")]
    public IActionResult GetCountryAndStates(bool? isActive)
    {
        var paramResultOut = DbHelper.CreateOutParameter("@Result", SqlDbType.NVarChar, 100);
        var paramIsActive = DbHelper.CreateParameter("@IsActive", isActive);
        //var paramResultOut = LiteX.DbHelper.MySql.DbHelper.CreateOutParameter("@Result", MySqlDbType.VarChar, 100);
        //var paramIsActive = LiteX.DbHelper.MySql.DbHelper.CreateParameter("@pIsActive", isActive);
        //var paramResultOut = LiteX.DbHelper.Npgsql.DbHelper.CreateOutParameter("@Result", NpgsqlDbType.Varchar, 100);
        //var paramIsActive = LiteX.DbHelper.Npgsql.DbHelper.CreateParameter("@pIsActive", isActive);
        var spName = "GetCountriesAndStates";

        var ds = _dbHelper.GetDataSet(spName, paramIsActive, paramResultOut);

        var countries = new List<Country>();

        // Convert DataTable to List
        foreach (DataRow row in ds.Tables[0].Rows)
        {
            countries.Add(new Country()
            {
                Id = (int)row["Id"],
                Name = (string)row["Name"],
                TwoLetterIsoCode = (string)row["TwoLetterIsoCode"],
                ThreeLetterIsoCode = (string)row["ThreeLetterIsoCode"],
                NumericIsoCode = (int)row["NumericIsoCode"],
                IsActive = (bool)row["IsActive"],
                DisplayOrder = (int)row["DisplayOrder"],
            });
        }

        var states = new List<StateProvince>();

        // Convert DataTable to List
        foreach (DataRow row in ds.Tables[1].Rows)
        {
            states.Add(new StateProvince()
            {
                Id = (int)row["Id"],
                CountryId = (int)row["CountryId"],
                Name = (string)row["Name"],
                Abbreviation = (string)row["Abbreviation"],
                IsActive = (bool)row["IsActive"],
                DisplayOrder = (int)row["DisplayOrder"],
            });
        }

        // NOT WORKING - Get out parameter value
        var resultOut = paramResultOut.Value;

        return Ok(new { Countries = countries, StateProvinces = states });
    }

    /// <summary>
    /// Get all country list (using DataReader)
    /// Weak type result
    /// </summary>
    /// <param name="isActive"></param>
    /// <param name="useStoredProc">Use stored procedure</param>
    /// <returns></returns>
    [HttpGet]
    [Route("get-all-using-datareader")]
    public IActionResult GetAllUsingDataReader(bool? isActive, bool useStoredProc = true)
    {
        var paramResultOut = DbHelper.CreateOutParameter("@Result", SqlDbType.NVarChar, 100);
        var paramIsActive = DbHelper.CreateParameter("@IsActive", isActive);
        //var paramResultOut = LiteX.DbHelper.MySql.DbHelper.CreateOutParameter("@Result", MySqlDbType.VarChar, 100);
        //var paramIsActive = LiteX.DbHelper.MySql.DbHelper.CreateParameter("@pIsActive", isActive);
        //var paramResultOut = LiteX.DbHelper.Npgsql.DbHelper.CreateOutParameter("@Result", NpgsqlDbType.Varchar, 100);
        //var paramIsActive = LiteX.DbHelper.Npgsql.DbHelper.CreateParameter("@pIsActive", isActive);

        var countries = new List<Country>();
        if (useStoredProc)
        {
            // Stored procedure
            var spName = "GetCountries";
            var dataReader = _dbHelper.ExecuteReder(spName, paramIsActive, paramResultOut);

            // parse DataReader data
            countries = dataReader.ParseCountryList();
        }
        else
        {
            // Raw sql commnad
            var cmdText = "SELECT * FROM Country WHERE IsActive = @IsActive";
            var dataReader = _dbHelper.ExecuteReder(cmdText, CommandType.Text, paramIsActive);

            // parse DataReader data
            countries = dataReader.ParseCountryList();
        }

        // NOT WORKING - Get out parameter value
        var resultOut = paramResultOut.Value;

        if (countries == null || countries.Count == 0)
            return NotFound("No record found");

        return Ok(countries);
    }

    /// <summary>
    /// Get all country list (using DataTable)
    /// Weak type result
    /// </summary>
    /// <param name="isActive"></param>
    /// <param name="useStoredProc">Use stored procedure</param>
    /// <returns></returns>
    [HttpGet]
    [Route("get-all-using-datatable")]
    public IActionResult GetAllUsingDataTable(bool? isActive, bool useStoredProc = true)
    {
        var paramResultOut = DbHelper.CreateOutParameter("@Result", SqlDbType.NVarChar, 100);
        var paramIsActive = DbHelper.CreateParameter("@IsActive", isActive);
        //var paramResultOut = LiteX.DbHelper.MySql.DbHelper.CreateOutParameter("@Result", MySqlDbType.VarChar, 100);
        //var paramIsActive = LiteX.DbHelper.MySql.DbHelper.CreateParameter("@pIsActive", isActive);
        //var paramResultOut = LiteX.DbHelper.Npgsql.DbHelper.CreateOutParameter("@Result", NpgsqlDbType.Varchar, 100);
        //var paramIsActive = LiteX.DbHelper.Npgsql.DbHelper.CreateParameter("@pIsActive", isActive);

        var countries = new List<Country>();
        if (useStoredProc)
        {
            // Stored procedure
            var spName = "GetCountries";
            var dataTable = _dbHelper.GetDataTable(spName, paramIsActive, paramResultOut);

            // parse DataTable data
            // Convert DataTable to List
            foreach (DataRow row in dataTable.Rows)
            {
                countries.Add(new Country()
                {
                    Id = (int)row["Id"],
                    Name = (string)row["Name"],
                    TwoLetterIsoCode = (string)row["TwoLetterIsoCode"],
                    ThreeLetterIsoCode = (string)row["ThreeLetterIsoCode"],
                    NumericIsoCode = (int)row["NumericIsoCode"],
                    IsActive = (bool)row["IsActive"],
                    DisplayOrder = (int)row["DisplayOrder"],
                });
            }
        }
        else
        {
            // Raw sql commnad
            var cmdText = "SELECT * FROM Country WHERE IsActive = @IsActive";
            var dataTable = _dbHelper.GetDataTable(cmdText, CommandType.Text, paramIsActive);

            // parse DataTable data
            // Convert DataTable to List
            foreach (DataRow row in dataTable.Rows)
            {
                countries.Add(new Country()
                {
                    Id = (int)row["Id"],
                    Name = (string)row["Name"],
                    TwoLetterIsoCode = (string)row["TwoLetterIsoCode"],
                    ThreeLetterIsoCode = (string)row["ThreeLetterIsoCode"],
                    NumericIsoCode = (int)row["NumericIsoCode"],
                    IsActive = (bool)row["IsActive"],
                    DisplayOrder = (int)row["DisplayOrder"],
                });
            }
        }

        // NOT WORKING - Get out parameter value
        var resultOut = paramResultOut.Value;

        if (countries == null || countries.Count == 0)
            return NotFound("No record found");

        return Ok(countries);
    }

    /// <summary>
    /// Get country by id (using DataReader)
    /// Weak type result
    /// </summary>
    /// <param name="id"></param>
    /// <param name="useStoredProc">Use stored procedure</param>
    /// <returns></returns>
    [HttpGet]
    [Route("get-by-id-using-datareader/{id}")]
    public IActionResult GetByIdUsingDataReader(int id, bool useStoredProc = true)
    {
        var paramResultOut = DbHelper.CreateOutParameter("@Result", SqlDbType.NVarChar, 100);
        var paramId = DbHelper.CreateParameter("@Id", id);
        //var paramResultOut = LiteX.DbHelper.MySql.DbHelper.CreateOutParameter("@Result", MySqlDbType.VarChar, 100);
        //var paramId = LiteX.DbHelper.MySql.DbHelper.CreateParameter("@pId", id);
        //var paramResultOut = LiteX.DbHelper.Npgsql.DbHelper.CreateOutParameter("@Result", NpgsqlDbType.Varchar, 100);
        //var paramId = LiteX.DbHelper.Npgsql.DbHelper.CreateParameter("@pId", id);

        var country = new Country();
        if (useStoredProc)
        {
            // Stored procedure
            var spName = "GetCountryById";
            var dataReader = _dbHelper.ExecuteReder(spName, paramId, paramResultOut);

            // parse DataReader data
            country = dataReader.ParseCountry();
        }
        else
        {
            // Raw sql commnad
            var cmdText = "SELECT * FROM Country WHERE Id = @Id";
            var dataReader = _dbHelper.ExecuteReder(cmdText, CommandType.Text, paramId);

            // parse DataReader data
            country = dataReader.ParseCountry();
        }

        // NOT WORKING - Get out parameter value
        var resultOut = paramResultOut.Value;

        if (country == null)
            return NotFound("No record found");

        return Ok(country);
    }

    #endregion
}
```


## Todo List :clipboard:

#### Database Providers

- [x] SqlServer
- [x] MySql
- [x] PostgreSql
- [x] MariaDB
- [x] Oracle

#### Basic API

- [x] ExecuteNonQuery
- [x] ExecuteScalar
- [x] GetDataTable
- [x] GetDataSet
- [x] ExecuteReder
- [x] CreateParameter


#### Coming soon
- Typesafe result
- Async support
- Pagination at DB server side
- Batch execution
- DbProviderFactory
- OUT parameter support in Stored Procedure
- Ping
- Many more


---



## Support :telephone:
> Reach out to me at one of the following places!

- Email :envelope: at <a href="mailto:toaashishpatel@gmail.com" target="_blank">`toaashishpatel@gmail.com`</a>
- NuGet :package: at <a href="https://www.nuget.org/profiles/iamaashishpatel" target="_blank">`@iamaashishpatel`</a>



## Authors :boy:

* **Ashish Patel** - [A-Patel](https://github.com/a-patel)


##### Connect with me

| Linkedin | GitHub | Facebook | Twitter | Instagram | Tumblr | Website |
|----------|----------|----------|----------|----------|----------|----------|
| [![linkedin](https://cdnjs.cloudflare.com/ajax/libs/foundicons/3.0.0/svgs/fi-social-linkedin.svg)](https://www.linkedin.com/in/iamaashishpatel) | [![github](https://cdnjs.cloudflare.com/ajax/libs/foundicons/3.0.0/svgs/fi-social-github.svg)](https://github.com/a-patel) | [![facebook](https://cdnjs.cloudflare.com/ajax/libs/foundicons/3.0.0/svgs/fi-social-facebook.svg)](https://www.facebook.com/aashish.mrcool) | [![twitter](https://cdnjs.cloudflare.com/ajax/libs/foundicons/3.0.0/svgs/fi-social-twitter.svg)](https://twitter.com/aashish_mrcool) | [![instagram](https://cdnjs.cloudflare.com/ajax/libs/foundicons/3.0.0/svgs/fi-social-instagram.svg)](https://www.instagram.com/iamaashishpatel/) | [![tumblr](https://cdnjs.cloudflare.com/ajax/libs/foundicons/3.0.0/svgs/fi-social-tumblr.svg)](https://iamaashishpatel.tumblr.com/) | [![website](https://cdnjs.cloudflare.com/ajax/libs/foundicons/3.0.0/svgs/fi-social-blogger.svg)](http://aashishpatel.co.nf/) |
| | | | | | |



## Donations :dollar:

[![Buy Me A Coffee](https://www.buymeacoffee.com/assets/img/custom_images/orange_img.png)](https://www.buymeacoffee.com/iamaashishpatel) 

[![Patreon](https://c5.patreon.com/external/logo/become_a_patron_button.png)](https://www.patreon.com/iamaashishpatel)



## License :lock:

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details

