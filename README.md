# Orm-Compare and benchmark

### Introduction
Scope of this project is to analyze different .NET approaches to retrieve data from MSSQL database and expose through a Web Api Rest. For semplicity only Get method will be considered but
interaction with database will use different ways and different technologies:

1. EntityFrameworkCore 2.1
2. EntityFrameworkCore 3.1
3. EntityFrameworkCore 5.0
4. EntityFrameworkCore 6.0
5. Dapper (.Net 6.0) [[info](https://github.com/DapperLib/Dapper)]
6. JsonPath (.Net 6.0) [[info](https://docs.microsoft.com/en-us/sql/relational-databases/json/json-path-expressions-sql-server?view=sql-server-2017)]

### Database project.
The database contains one table with five fields:

```
CREATE TABLE [dbo].[portfolio] (`
    [PortfolioId]     INT            NOT NULL,
    [PortfolioCode]   NVARCHAR (100) NOT NULL,
    [PortfolioName]   NVARCHAR (100) NOT NULL,
    [PortfolioType]   NVARCHAR (100) NOT NULL,
    [PortfolioStatus] NVARCHAR (100) NOT NULL
);
```

The table contains 20,000 rows.

### Structure of the project
The solution contains six projects for each technology plus a database project. All Web Api projects have the same structure:
1. Controller contains the controller
2. Models contains class that maps the database table
3. Services contains the repository patter
4. MyContext is the database context 

##### Steps for Entity Framework Core projects.
1. Add *Microsoft.EntityFrameworkCore* by *NuGet* package to your project.
2. Update ConfigureServices in *Startup* class.
3. Create your model, *Portfolio* class, **ensuring you will add a primary key**.
4. Create your own context, *MyContext* class, inherited from DbContext where you set your data set. This class will use your model.
5. Create your repository class by injecting your context class. This class will have only one method, * GetPortfolios()*.
6. Creat your resourse in *PortfolioController*.

##### Steps for Dapper project:
1. Add *Dapper* by NuGet package to your project.
2. Create your model, *Portfolio* class.
3. Create your resourse in *PortfolioController*.

##### Steps for Json Path project:
1. Create your resourse in *PortfolioController*. It is only needed to pass the query you need. That's all.


### How to test
In order to test web api's, the response time will be considered. I created a Benchmark Rest Get project that I used for this purpose and can be found [here](https://github.com/skepee/Benchmark-Rest-Api-Get). It is a console app that you can invoke in the command line by passing these parameters:
1. Web Api url
2. number of iterations
3. Y/N if you want a log file, optional

### Benchmark results

The benchmark results are here proposed in two ways. One is by using a tool I created for http Get requests [Benchmark Rest Get](https://github.com/skepee/Benchmark-Rest-Api-Get) and another one is by using [Crank](https://github.com/dotnet/crank). Let's see.


#### Benchmark by using BenchmarkRestGet
[Benchmark Rest Get](https://github.com/skepee/Benchmark-Rest-Api-Get) tool can be used through console. I launched the API and then run it locally setting on 5000 iterations in this way:

```
BenchmarkRestGet https://localhost:44347/api/portfolio/ef3_1 5000 Y
BenchmarkRestGet https://localhost:44323/api/portfolio/ef5_0 5000 Y
BenchmarkRestGet https://localhost:44397/api/portfolio/ef2_1 5000 Y
BenchmarkRestGet https://localhost:7266/api/portfolio/dapper 5000 Y
BenchmarkRestGet https://localhost:7230/api/portfolio/ef6_0 5000 Y
BenchmarkRestGet https://localhost:7113/api/portfolio/jsonpath 5000 Y
```

These are the results for the *GetPortfolios()* calling on 5000 iterations to get 20,000 rows each time:

<table>
    <tr>
        <th>Type</th>
        <th>Min Time</th>
        <th>Max Time</th>
        <th>Avg Time</th>
        <th>Initial memory allocation</th>
        <th>Memory allocation (after 10 times)</th>
        <th><a href="https://github.com/skepee/Benchmark-Rest-Api-Get">Benchmark</a></th>
    </tr>
    <tr>
        <td>EF 2.1</td>
        <td>148 ms</td>
        <td>5142 ms</td>
        <td>407.9812 ms</td>
        <td><img src="https://github.com/skepee/Orm-Compare/blob/master/OrmCompare/JsonEF_2_1/Benchmark/EF2.1InitialMemAll.jpg" heigth="100px" width="200px"></td>
        <td><img src="https://github.com/skepee/Orm-Compare/blob/master/OrmCompare/JsonEF_2_1/Benchmark/EF2.1MemAll10Iterations.jpg" heigth="100px" width="200px"></td>
        <td><a href="https://github.com/skepee/Orm-Compare/blob/master/OrmCompare/JsonEF_2_1/Benchmark/BenchmarkGetRest_ef2_1.txt">Benchmark EF 2.1</a></td>
    </tr> 
    <tr>
        <td>EF 3.1</td>
        <td>117 ms</td>
        <td>7126 ms</td>
        <td>375.5712 ms</td>
        <td><img src="https://github.com/skepee/Orm-Compare/blob/master/OrmCompare/JsonEF_3_1/Benchmark/EF3.1InitialMemAll.jpg" heigth="100px" width="200px"></td>
        <td><img src="https://github.com/skepee/Orm-Compare/blob/master/OrmCompare/JsonEF_3_1/Benchmark/EF3.1MemAll10Iterations.jpg" heigth="100px" width="200px"></td>
        <td><a href="https://github.com/skepee/Orm-Compare/blob/master/OrmCompare/JsonEF_3_1/Benchmark/BenchmarkGetRest_ef3_1.txt">Benchmark EF 3.1</a></td>
    </tr> 
    <tr>
        <td>EF 5.0</td>
        <td>103 ms</td>
        <td>1636 ms</td>
        <td>277.197 ms</td>
        <td><img src="https://github.com/skepee/Orm-Compare/blob/master/OrmCompare/JsonEF_5_0/Benchmark/EF5.0InitialMemAll.jpg" heigth="100px" width="200px"></td>
        <td><img src="https://github.com/skepee/Orm-Compare/blob/master/OrmCompare/JsonEF_5_0/Benchmark/EF5.0MemAll10Iterations.jpg" heigth="100px" width="200px"></td>
        <td><a href="https://github.com/skepee/Orm-Compare/blob/master/OrmCompare/JsonEF_5_0/Benchmark/BenchmarkGetRest_ef5_0.txt">Benchmark EF 5.0</a></td>
    </tr> 
    <tr>
        <td>EF 6.0</td>
        <td>79 ms</td>
        <td>983 ms</td>
        <td>195.111 ms</td>
        <td><img src="https://github.com/skepee/Orm-Compare/blob/master/OrmCompare/JsonEF_6_0/Benchmark/EF6.0InitialMemAll.jpg" heigth="100px" width="200px"></td>
        <td><img src="https://github.com/skepee/Orm-Compare/blob/master/OrmCompare/JsonEF_6_0/Benchmark/EF6.0MemAll10Iterations.jpg" heigth="100px" width="200px"></td>
        <td><a href="https://github.com/skepee/Orm-Compare/blob/master/OrmCompare/JsonEF_6_0/Benchmark/BenchmarkGetRest_ef6_0.txt">Benchmark EF 6.0</a></td>
    </tr> 
    <tr>
        <td>Dapper (.NET 6.0)</td>
        <td>60 ms</td>
        <td>7012 ms</td>
        <td>135.8858 ms</td>
        <td><img src="https://github.com/skepee/Orm-Compare/blob/master/OrmCompare/JsonDapper_6_0/Benchmark/DapperInitialMemAll.jpg" heigth="100px" width="200px"></td>
        <td><img src="https://github.com/skepee/Orm-Compare/blob/master/OrmCompare/JsonDapper_6_0/Benchmark/DapperMemAll10Iterations.jpg" heigth="100px" width="200px"></td>
        <td><a href="https://github.com/skepee/Orm-Compare/blob/master/OrmCompare/JsonDapper_6_0/Benchmark/BenchmarkGetRest_Dapper.Net6.0.txt">Benchmark EF 2.1</a></td>
    </tr> 
    <tr>
        <td>JsonPath (.NET 6.0)</td>
        <td>87 ms</td>
        <td>527 ms</td>
        <td>183.673 ms</td>
        <td><img src="https://github.com/skepee/Orm-Compare/blob/master/OrmCompare/JsonPath_6_0/Benchmark/JsonPathInitialMemAll.jpg" heigth="100px" width="200px"></td>
        <td><img src="https://github.com/skepee/Orm-Compare/blob/master/OrmCompare/JsonPath_6_0/Benchmark/JsonPathMemAll10Iterations.jpg" heigth="100px" width="200px"></td>
        <td><a href="https://github.com/skepee/Orm-Compare/blob/master/OrmCompare/JsonPath_6_0/Benchmark/BenchmarkGetRest_JsonPath.Net6.0.txt">Benchmark Json Path</a></td>
    </tr> 
</table>
<br/>
<p>Graph result for Average respone time:</p>
<img src="https://github.com/skepee/Orm-Compare/blob/master/OrmCompare/graphresult.jpg">

#### Benchmark with [Crank](https://github.com/dotnet/crank)
By using Crank ([here](https://www.youtube.com/watch?v=2IgfrnG-128) you can find a guide) you do not need to launch the project but just setting it in a .yml file and you can run it locally. These the results:

| application           |    EF 6.0     |    EF 5.0      |    EF 3.1      |     Dapper    |    JsonPath   |
| --------------------- | ------------- | -------------- |--------------  | ------------- | ------------- |
| CPU Usage (%)         | 14            | 15             | 15             | 18            | 23            |
| Cores usage (%)       | 112           | 117            | 119            | 145           | 183           |
| Working Set (MB)      | 155           | 115            | 112            | 108           | 146           |
| Private Memory (MB)   | 172           | 129            | 132            | 126           | 163           |
| Build Time (ms)       | 3,341         | 3,677          | 3,501          | 3,237         | 2,225         |
| Start Time (ms)       | 666           | 666            | 17,145         | 16,327        | 703           |
| Published Size (KB)   | 104,948       | 99,572         | 92,669         | 96,567        | 96,369        |
| .NET Core SDK Version | 6.0.100       | 5.0.403        | 3.1.415        | 6.0.100       | 6.0.100       |
| ASP.NET Core Version  | 6.0.0+ae1a6cb | 5.0.12+0bc3c37 | 3.1.21+458d974 | 6.0.0+ae1a6cb | 6.0.0+ae1a6cb |
| .NET Runtime Version  | 6.0.0+4822e3c | 5.0.12+7211aa0 | 3.1.21+df8abc0 | 6.0.0+4822e3c | 6.0.0+4822e3c |


| load                | EF 6.0  | EF 5.0  |  EF 3.1 | Dapper  | JsonPath|
| ------------------- | ------- |-------  | ------- | ------- | ------- |
| CPU Usage (%)       | 7       | 8       | 6       | 7       | 6       |
| Cores usage (%)     | 55      | 65      | 48      | 60      | 46      |
| Working Set (MB)    | 29      | 29      | 29      | 29      | 29      |
| Private Memory (MB) | 39      | 40      | 40      | 40      | 40      |
| Start Time (ms)     | 130     | 123     | 72      | 70      | 78      |
| First Request (ms)  | 101     | 107     | 102     | 101     | 112     |
| Requests            | 99,537  | 97,077  | 104,187 | 101,427 | 97,117  |
| Bad responses       | 99,537  | 97,077  | 104,187 | 101,427 | 97,117  |
| Mean latency (us)   | 38,663  | 39,671  | 36,947  | 37,980  | 39,639  |
| Max latency (us)    | 746,205 | 759,678 | 693,422 | 748,605 | 624,703 |
| Requests/sec        | 6,607   | 6,454   | 6,926   | 6,728   | 6,452   |
| Requests/sec (max)  | 44,419  | 40,508  | 41,411  | 43,592  | 43,768  |

... and finally the trace screenshots created in the crank job:

<table>
    <tr>
        <th>Type</th>
        <th>Trace results</th>
    </tr>
    <tr>
        <td>EF 3.1</td>
        <td><img src="https://github.com/skepee/Orm-Compare/blob/master/Crank Trace results/OrmCompareTraceEF3.1.jpg"></td>
    </tr> 
    <tr>
        <td>EF 5.0</td>
        <td><img src="https://github.com/skepee/Orm-Compare/blob/master/Crank Trace results/OrmCompareTraceEF5.0.jpg"></td>
    </tr> 
    <tr>
        <td>EF 6.0</td>
        <td><img src="https://github.com/skepee/Orm-Compare/blob/master/Crank Trace results/OrmCompareTraceEF6.0.jpg"></td>
    </tr> 
    <tr>
        <td>Dapper (.NET 6.0)</td>
        <td><img src="https://github.com/skepee/Orm-Compare/blob/master/Crank Trace results/OrmCompareTraceDapper.jpg"></td>
    </tr> 
    <tr>
        <td>JsonPath (.NET 6.0)</td>
        <td><img src="https://github.com/skepee/Orm-Compare/blob/master/Crank Trace results/OrmCompareTraceJsonPath.jpg"></td>
    </tr> 
</table>


