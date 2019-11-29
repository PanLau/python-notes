###### Create by:along

### read_table(filepath_or_buffer[, sep, …])
>Read general delimited file into DataFrame

### read_csv(filepath_or_buffer[, sep, …])
>Read CSV (comma-separated) file into DataFrame

### read_excel(io[, sheet_name, header, names, …])
>Read an Excel table into a pandas DataFrame

### read_json([path_or_buf, orient, typ, dtype, …])
>Convert a JSON string to pandas object

### read_html(io[, match, flavor, header, …])
>Read HTML tables into a list of DataFrame objects

### read_sql(sql, con[, index_col, …])
>Read SQL query or database table into a DataFrame

### read_sql_table(table_name, con[, schema, …])
>Read SQL database table into a DataFrame

### read_sql_query(sql, con[, index_col, …])
>Read SQL query into a DataFrame

### read_pickle(path[, compression])
>Load pickled pandas object (or any object) from file

### read_fwf(filepath_or_buffer[, colspecs, widths])
>Read a table of fixed-width formatted lines into DataFrame

### read_msgpack(path_or_buf[, encoding, iterator])
>Load msgpack pandas object from the specified file path

### read_clipboard([sep])
>Read text from clipboard and pass to read_table

### read_hdf(path_or_buf[, key, mode])
>Read from the store, close it if we opened it

### read_feather(path[, nthreads])
>Load a feather-format object from the file path

### read_parquet(path[, engine, columns])
>Load a parquet object from the file path, returning a DataFrame

### read_sas(filepath_or_buffer[, format, …])
>Read SAS files stored as either XPORT or SAS7BDAT format files


### read_gbq(query[, project_id, index_col, …])
>Load data from Google BigQuery

### read_stata(filepath_or_buffer[, …])
>Read Stata file into DataFrame