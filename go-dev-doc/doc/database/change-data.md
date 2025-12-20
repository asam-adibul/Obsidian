# Executing SQL statements that don't return data

When you perform database actions that don’t return data, use an `Exec` or `ExecContext` method from the `database/sql` package. SQL statements you’d execute this way include `INSERT`, `DELETE`, and `UPDATE`.

When your query might return rows, use a `Query` or `QueryContext` method instead. For more, see [Querying a database](/doc/database/querying).

An `ExecContext` method works as an `Exec` method does, but with an additional `context.Context` argument, as described in [Canceling in-progress operations](/doc/database/cancel-operations).

Code in the following example uses [`DB.Exec`](https://pkg.go.dev/database/sql#DB.Exec) to execute a statement to add a new record album to an `album` table.

```
func AddAlbum(alb Album) (int64, error) {
    result, err := db.Exec("INSERT INTO album (title, artist) VALUES (?, ?)", alb.Title, alb.Artist)
    if err != nil {
        return 0, fmt.Errorf("AddAlbum: %v", err)
    }

    // Get the new album's generated ID for the client.
    id, err := result.LastInsertId()
    if err != nil {
        return 0, fmt.Errorf("AddAlbum: %v", err)
    }
    // Return the new album's ID.
    return id, nil
}
```

`DB.Exec` returns values: an [`sql.Result`](https://pkg.go.dev/database/sql#Result) and an error. When the error is `nil`, you can use the `Result` to get the ID of the last inserted item (as in the example) or to retrieve the number of rows affected by the operation.

**Note:** Parameter placeholders in prepared statements vary depending on the DBMS and driver you’re using. For example, the [pq driver](https://pkg.go.dev/github.com/lib/pq) for Postgres requires a placeholder like `$1` instead of `?`.

If your code will be executing the same SQL statement repeatedly, consider using an `sql.Stmt` to create a reusable prepared statement from the SQL statement. For more, see [Using prepared statements](/doc/database/prepared-statements).

**Caution:** Don’t use string formatting functions such as `fmt.Sprintf` to assemble an SQL statement! You could introduce an SQL injection risk. For more, see [Avoiding SQL injection risk](/doc/database/sql-injection).

#### Functions for executing SQL statements that don’t return rows

Function

Description

`[DB.Exec](https://pkg.go.dev/database/sql#DB.Exec)`  
`[DB.ExecContext](https://pkg.go.dev/database/sql#DB.ExecContext)`

Execute a single SQL statement in isolation.

`[Tx.Exec](https://pkg.go.dev/database/sql#Tx.Exec)`  
`[Tx.ExecContext](https://pkg.go.dev/database/sql#Tx.ExecContext)`

Execute a SQL statement within a larger transaction. For more, see [Executing transactions](/doc/database/execute-transactions).

`[Stmt.Exec](https://pkg.go.dev/database/sql#Stmt.Exec)`  
`[Stmt.ExecContext](https://pkg.go.dev/database/sql#Stmt.ExecContext)`

Execute an already-prepared SQL statement. For more, see [Using prepared statements](/doc/database/prepared-statements).

`[Conn.ExecContext](https://pkg.go.dev/database/sql#Conn.ExecContext)`

For use with reserved connections. For more, see [Managing connections](/doc/database/manage-connections).