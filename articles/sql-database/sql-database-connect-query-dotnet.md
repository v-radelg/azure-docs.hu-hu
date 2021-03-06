---
title: "Csatlakozás az Azure SQL Database-hez .NET (C#) használatával | Microsoft Docs"
description: "Az ebben a rövid útmutatóban található mintakódot arra használhatja, hogy létrehozzon egy C# nyelven írt modern alkalmazást, amelyet egy nagy teljesítményű, felhőalapú, Azure SQL Database relációs adatbázis támogat."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 7faca033-24b4-4f64-9301-b4de41e73dfd
ms.service: sql-database
ms.custom: development
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 03/16/2017
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: c54ccef3098502c9fbaad13c5fe35ed15bf93f29
ms.lasthandoff: 03/21/2017


---
# <a name="azure-sql-database-use-net-c-to-connect-and-query-data"></a>Azure SQL Database: Csatlakozás és adatlekérdezés a .NET (C#) használatával

A [C# és az ADO.NET](https://msdn.microsoft.com/library/kb9s9ks0.aspx) használatával csatlakozhat egy Azure SQL-adatbázishoz, és lekérdezéseket hajthat végre. Ez az útmutató ismerteti, hogyan használható a C# az Azure SQL-adatbázishoz való csatlakozásra, majd a lekérdezési, beszúrási, frissítési és törlési utasítások végrehajtására.

Ez a rövid útmutató az alábbi rövid útmutatók egyikében létrehozott erőforrásokat használja kiindulási pontnak:

- [DB létrehozása – portál](sql-database-get-started-portal.md)
- [DB létrehozása – CLI](sql-database-get-started-cli.md)
- [DB létrehozása – PowerShell](sql-database-get-started-powershell.md) 

A kezdés előtt győződjön meg róla, hogy konfigurálta a fejlesztési környezetet a C#-hoz. Tekintse meg a [Visual Studio Community ingyenes telepítését ismertető](https://www.visualstudio.com/) oldalt vagy telepítse az [ADO.NET illesztőprogramot az SQL Serverhez](https://www.microsoft.com/net/download).

## <a name="connect-to-database-and-query-data"></a>Csatlakozás az adatbázishoz és adatok lekérdezése

Kérje le a kapcsolati karakterláncot az Azure Portalon. A kapcsolati karakterlánccal csatlakozhat az Azure SQL adatbázishoz.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).
2. Válassza az **SQL-adatbázisok** elemet a bal oldali menüben, majd kattintson az új adatbázisra az **SQL-adatbázisok** oldalon. 
3. Az adatbázis **Alapvető erőforrások** paneljében keresse meg a **Adatbázis-kapcsolati karakterláncok megjelenítése** elemet, majd kattintson rá.
4. Másolja az **ADO.NET** kapcsolati karakterláncot.

    <img src="./media/sql-database-connect-query-dotnet/connection-strings.png" alt="connection strings" style="width: 780px;" />

5. Nyissa meg a Visual Studiót, és hozzon létre egy konzolalkalmazást.
6. Adja hozzá a ```using System.Data.SqlClient``` sort a kódfájlhoz ([System.Data.SqlClient névtér](https://msdn.microsoft.com/library/system.data.sqlclient.aspx)). 

7. Az [SqlCommand.ExecuteReader](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.executereader.aspx) metódus és egy [SELECT](https://msdn.microsoft.com/library/ms189499.aspx) Transact-SQL utasítás együttes használatával lekérdezheti az adatokat az Azure SQL-adatbázisban.

    ```csharp
    string strConn = "<connection string>";
    using (var connection = new SqlConnection(strConn))
    {
   connection.Open();

    SqlCommand selectCommand = new SqlCommand("", connection);
    selectCommand.CommandType = CommandType.Text;

    selectCommand.CommandText = @"SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
        FROM [SalesLT].[ProductCategory] pc
        JOIN [SalesLT].[Product] p
        ON pc.productcategoryid = p.productcategoryid";

    SqlDataReader reader = selectCommand.ExecuteReader();

    while (reader.Read())
    {
        // show data
        Console.WriteLine($"{reader.GetString(0)}\t{reader.GetString(1)}");
    }
    reader.Close();
    }
    ```

## <a name="insert-data"></a>Adat beszúrása

Az [SqlCommand.ExecuteNonQuery](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.executenonquery.aspx) metódus és egy [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) Transact-SQL utasítás együttes használatával beszúrhatja az adatokat az Azure SQL-adatbázisba.

```csharp
SqlCommand insertCommand = new SqlCommand("", connection);
insertCommand.CommandType = CommandType.Text;
insertCommand.CommandText = @"INSERT INTO[SalesLT].[Product]
            ( [Name]
            , [ProductNumber]
            , [Color]
            , [StandardCost]
            , [ListPrice]
            , [SellStartDate]
            )
VALUES
(
            @Name,
            @ProductNumber,
            @Color,
            @StandardCost,
            @ListPrice,
            @SellStartDate)";

            insertCommand.Parameters.AddWithValue("@Name", "BrandNewProduct");
            insertCommand.Parameters.AddWithValue("@ProductNumber", "200989");
            insertCommand.Parameters.AddWithValue("@Color", "Blue");
            insertCommand.Parameters.AddWithValue("@StandardCost", 75);
            insertCommand.Parameters.AddWithValue("@ListPrice", 80);
            insertCommand.Parameters.AddWithValue("@SellStartDate", "7/1/2016");

int newrows = insertCommand.ExecuteNonQuery();
Console.WriteLine($"Inserted {newrows.ToString()} row(s).");
```

## <a name="update-data"></a>Adatok frissítése

Az [SqlCommand.ExecuteNonQuery](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.executenonquery.aspx) metódus és egy [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) Transact-SQL utasítás együttes használatával frissítheti az adatokat az Azure SQL-adatbázisban.

```csharp
SqlCommand updateCommand = new SqlCommand("", connection);
updateCommand.CommandType = CommandType.Text;
updateCommand.CommandText = @"UPDATE SalesLT.Product SET ListPrice = @ListPrice WHERE Name = @Name";
updateCommand.Parameters.AddWithValue("@Name", "BrandNewProduct");
updateCommand.Parameters.AddWithValue("@ListPrice", 500);

int updatedrows = updateCommand.ExecuteNonQuery();
Console.WriteLine($"Updated {updatedrows.ToString()} row(s).");
```

## <a name="delete-data"></a>Adat törlése

Az [SqlCommand.ExecuteNonQuery](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.executenonquery.aspx) metódus és egy [DELETE](https://msdn.microsoft.com/library/ms189835.aspx) Transact-SQL utasítás együttes használatával törölheti az adatokat az Azure SQL-adatbázisban.

```csharp
SqlCommand deleteCommand = new SqlCommand("", connection);
deleteCommand.CommandType = CommandType.Text;
deleteCommand.CommandText = @"DELETE FROM SalesLT.Product WHERE Name = @Name";
deleteCommand.Parameters.AddWithValue("@Name", "BrandNewProduct");

int deletedrows = deleteCommand.ExecuteNonQuery();
Console.WriteLine($"Deleted {deletedrows.ToString()} row(s).");
```

## <a name="complete-script"></a>Teljes szkript

A következő szkript egyetlen kódrészletben tartalmazza az összes előző lépést.

```csharp
using System;
using System.Data;
using System.Data.SqlClient;

namespace ConsoleApplication1
{
    class Program
    {
        static void Main(string[] args)
        {

            string strConn = "<connection string>";

            using (var connection = new SqlConnection(strConn))
            {
                connection.Open();

                Console.WriteLine("Query data example:");
                Console.WriteLine("\n=========================================\n");

                SqlCommand selectCommand = new SqlCommand("", connection);
                selectCommand.CommandType = CommandType.Text;

                selectCommand.CommandText = @"SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
                   FROM [SalesLT].[ProductCategory] pc
                   JOIN [SalesLT].[Product] p
                   ON pc.productcategoryid = p.productcategoryid";

                SqlDataReader reader = selectCommand.ExecuteReader();

                while (reader.Read())
                {
                    // show data columns
                    Console.WriteLine($"{reader.GetString(0)}\t{reader.GetString(1)}");
                }
                reader.Close();
                Console.WriteLine("\nPress any key to continue ...");
                Console.ReadLine();

                Console.WriteLine("\nInsert data example:");
                Console.WriteLine("=========================================\n");
                SqlCommand insertCommand = new SqlCommand("", connection);
                insertCommand.CommandType = CommandType.Text;
                insertCommand.CommandText = @"INSERT INTO[SalesLT].[Product]
                          ( [Name]
                          , [ProductNumber]
                          , [Color]
                          , [StandardCost]
                          , [ListPrice]
                          , [SellStartDate]
                          )
                VALUES
                (
                            @Name,
                            @ProductNumber,
                            @Color,
                            @StandardCost,
                            @ListPrice,
                            @SellStartDate)";

                insertCommand.Parameters.AddWithValue("@Name", "BrandNewProduct");
                insertCommand.Parameters.AddWithValue("@ProductNumber", "200989");
                insertCommand.Parameters.AddWithValue("@Color", "Blue");
                insertCommand.Parameters.AddWithValue("@StandardCost", 75);
                insertCommand.Parameters.AddWithValue("@ListPrice", 80);
                insertCommand.Parameters.AddWithValue("@SellStartDate", "7/1/2016");

                int newrows = insertCommand.ExecuteNonQuery();
                Console.WriteLine($"Inserted {newrows.ToString()} row(s).");
                Console.WriteLine("\nPress any key to continue ...");
                Console.ReadLine();

                Console.WriteLine("\nUpdate data example:");
                Console.WriteLine("======================\n");
                SqlCommand updateCommand = new SqlCommand("", connection);
                updateCommand.CommandType = CommandType.Text;
                updateCommand.CommandText = @"UPDATE SalesLT.Product SET ListPrice = @ListPrice WHERE Name = @Name";
                updateCommand.Parameters.AddWithValue("@Name", "BrandNewProduct");
                updateCommand.Parameters.AddWithValue("@ListPrice", 500);

                int updatedrows = updateCommand.ExecuteNonQuery();
                Console.WriteLine($"Updated {updatedrows.ToString()} row(s).");
                Console.WriteLine("\nPress any key to continue ...");
                Console.ReadLine();

                Console.WriteLine("\nDelete data example:");
                Console.WriteLine("======================\n");
                SqlCommand deleteCommand = new SqlCommand("", connection);
                deleteCommand.CommandType = CommandType.Text;
                deleteCommand.CommandText = @"DELETE FROM SalesLT.Product WHERE Name = @Name";
                deleteCommand.Parameters.AddWithValue("@Name", "BrandNewProduct");

                int deletedrows = deleteCommand.ExecuteNonQuery();
                Console.WriteLine($"Deleted {deletedrows.ToString()} row(s).");
                Console.WriteLine("\nPress any key to continue ...");
                Console.ReadLine();
            }
        }
    }
}
```

## <a name="next-steps"></a>Következő lépések

- A .NET dokumentációját megtekintheti itt: [.NET-dokumentáció](https://docs.microsoft.com/dotnet/).
- A Visual Studio Code-ból végzett lekérdezésről és adatszerkesztés a [Visual Studio Code-ot](https://code.visualstudio.com/docs) leíró cikkben olvashat.

