# 批量修改存储过程或视图

1. 编辑代码（修改@ProcName和中间代码）
```TSQL
DECLARE @DBName VARCHAR(200);
DECLARE @ProcName VARCHAR(200)='存储过程或视图名称';  -- 已存在的存储过程或视图（不存在则跳过操作）
DECLARE C_TABLES CURSOR FAST_FORWARD FOR
  -- 获取全部数据库
  SELECT NAME
    FROM MASTER..SYSDATABASES
    ORDER BY NAME;

OPEN C_TABLES;
  FETCH NEXT FROM C_TABLES INTO @DBName;
  WHILE @@FETCH_STATUS=0
  BEGIN
  
    -- 不能使用OBJECTPROPERTY方法和sysobjects表判断存储过程，必须先USE数据库后才能查到
    IF OBJECT_ID('[' + @DBName + '].[dbo].[' + @ProcName + ']') IS NOT NULL
    BEGIN
      PRINT @DBName;  -- 打印成功操作数据库，错误可在信息查看（普通错误直接提示，致命错误直接终止）
      EXEC('
      USE [' + @DBName + '];
      EXEC(''
        -- 单引号1个换4个

        -- ========================================================
        -- 请替换此处代码即可
        -- DEMO：
        ALTER PROC [dbo].[' + @ProcName + ']
            @BillGuid char(36)
        AS
        SELECT * FROM Bill WHERE BillGuid=@BillGuid;
        -- ========================================================

      '')
      ');
    END;
    FETCH NEXT FROM C_TABLES INTO @DBName;
  END
CLOSE C_TABLES;
DEALLOCATE C_TABLES;
```

2. 执行命令

[批量修改存储过程视图](https://www.cnblogs.com/hcbin/p/10788988.html)
