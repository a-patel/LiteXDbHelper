# LiteXDbHelper
> LiteXDbHelper is simple yet powerful and very high-performance DBHelper Class for different database providers like SqlServer, MySql, PostgreSql, MariaDB, Oracle in C#


## Database Providers :books:
- [SqlServer](docs/SqlServer.md)
- [MySql](docs/MySql.md)
- [PostgreSql](docs/PostgreSql.md)
- [MariaDB](docs/MariaDB.md)
- [Oracle](docs/Oracle.md)


## Features :pager:
- ExecuteNonQuery
- ExecuteScalar
- GetDataTable
- GetDataSet
- ExecuteReder
- CreateParameters (for each providers)

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

        #endregion

        #region Ctor

        /// <summary>
        /// Ctor
        /// </summary>
        public CountryController(IConfiguration configuration)
        {
            _connectionString = configuration.GetConnectionString("LiteXConnection");
            _dbHelper = new SqlHelper(_connectionString);
            //_dbHelper = new MySqlHelper(_connectionString);
            //_dbHelper = new NpgSqlHelper(_connectionString);
            //_dbHelper = new MariaDBHelper(_connectionString);
            //_dbHelper = new OracleHelper(_connectionString);
        }

        #endregion

        #region Methods

        [HttpGet]
        [Route("get-all")]
        public IActionResult GetAll(bool? isActive)
        {
            var paramResultOut = DbHelper.CreateOutParameter("@Result", SqlDbType.NVarChar, 100);
            var paramIsActive = DbHelper.CreateParameter("@IsActive", isActive);


            var spName = "GetCountries";
            //var countries = _dbHelper.ExecuteReder(spName, CommandType.StoredProcedure, r => r.ParseCountryList(), paramIsActive);
            var dataReader = _dbHelper.ExecuteReder(spName, paramIsActive, paramResultOut);

            //// raw sql commnad
            //var cmdText = "SELECT * FROM Country";
            //var dataReader = _dbHelper.ExecuteReder(cmdText, CommandType.Text, paramIsActive);

            var countries = dataReader.ParseCountryList();

            // way 3
            //var countries = _dbHelper.ExecuteReder(cmdText, CommandType.Text, r => r.TranslateAsCountry(), paramIsActive);

            var resultOut = paramResultOut.Value;

            if (countries == null || countries.Count == 0)
                return NotFound("No record found");

            return Ok(countries);
        }


        [HttpGet]
        [Route("get-by-id/{id}")]
        public IActionResult GetById(int id)
        {
            var paramResultOut = DbHelper.CreateOutParameter("@Result", SqlDbType.NVarChar, 100);
            var paramId = DbHelper.CreateParameter("@Id", id);

            var spName = "GetCountryById";
            //var country = _dbHelper.ExecuteReder(spName, r => r.ParseCountry(), paramId);
            var dataReader = _dbHelper.ExecuteReder(spName, paramId, paramResultOut);

            //if (country == null)
            //    return NotFound("No record found");

            //return Ok(country);
            return Ok();
        }


        [HttpPost]
        [Route("add")]
        public IActionResult Add(Country model)
        {
            var paramResultOut = DbHelper.CreateOutParameter("@Result", SqlDbType.NVarChar, 100);

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


            var spName = "AddCountry";
            var result = _dbHelper.ExecuteScalar(spName, dbParams);
            
            var resultOut = paramResultOut.Value;

            //if(result > 0)
            //    return Ok(result);

            return Ok();
        }


        [HttpPut]
        [Route("update")]
        public IActionResult Update(Country model)
        {
            var spName = "UpdateCountry";
            var paramResultOut = DbHelper.CreateOutParameter("@Result", SqlDbType.NVarChar, 100);

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


            var result = _dbHelper.ExecuteScalar(spName, dbParams);

            var resultOut = paramResultOut.Value;

            return Ok(result);
        }


        [HttpDelete]
        [Route("remove/{id}")]
        public IActionResult Remove(int id)
        {
            var spName = "DeleteCountry";
            var paramResultOut = DbHelper.CreateOutParameter("@Result", SqlDbType.NVarChar, 100);
            var paramId = DbHelper.CreateParameter("Id", id);
            var resultCount = _dbHelper.ExecuteNonQuery(spName, paramId, paramResultOut);

            if(resultCount  > 0)
                return Ok(resultCount);

            return StatusCode(500, "Error");
        }


        [HttpGet]
        [Route("get-country-and-states")]
        public IActionResult GetCountryAndStates()
        {
            var spName = "GetCountriesAndStates";
            var paramResultOut = DbHelper.CreateOutParameter("@Result", SqlDbType.NVarChar, 100);
            var ds = _dbHelper.GetDataSet(spName, paramResultOut);

            var countries = new List<Country>();
            var states = new List<StateProvince>();

            return Ok();
        }

        #endregion

        #region Utilities

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
- DbProviderFactory
- Ping
- OUT parameter support in Stored Procedure
- Typesafe result


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

