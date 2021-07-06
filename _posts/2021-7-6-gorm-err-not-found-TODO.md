---
layout: post
title: 从源码分析gorm何时抛出ErrRecordNotFound
---


```
// First find first record that match given conditions, order by primary key
func (db *DB) First(dest interface{}, conds ...interface{}) (tx *DB) {
	tx = db.Limit(1).Order(clause.OrderByColumn{
		Column: clause.Column{Table: clause.CurrentTable, Name: clause.PrimaryKey},
	})
	if len(conds) > 0 {
		tx.Statement.AddClause(clause.Where{Exprs: tx.Statement.BuildCondition(conds[0], conds[1:]...)})
	}
	tx.Statement.RaiseErrorOnNotFound = true // 提升ErrorOnNotFound
	tx.Statement.Dest = dest
	tx.callbacks.Query().Execute(tx)
	return
}

// Take return a record that match given conditions, the order will depend on the database implementation
func (db *DB) Take(dest interface{}, conds ...interface{}) (tx *DB) {
	tx = db.Limit(1)
	if len(conds) > 0 {
		tx.Statement.AddClause(clause.Where{Exprs: tx.Statement.BuildCondition(conds[0], conds[1:]...)})
	}
	tx.Statement.RaiseErrorOnNotFound = true // 提升ErrorOnNotFound
	tx.Statement.Dest = dest
	tx.callbacks.Query().Execute(tx)
	return
}

// Last find last record that match given conditions, order by primary key
func (db *DB) Last(dest interface{}, conds ...interface{}) (tx *DB) {
	tx = db.Limit(1).Order(clause.OrderByColumn{
		Column: clause.Column{Table: clause.CurrentTable, Name: clause.PrimaryKey},
		Desc:   true,
	})
	if len(conds) > 0 {
		tx.Statement.AddClause(clause.Where{Exprs: tx.Statement.BuildCondition(conds[0], conds[1:]...)})
	}
	tx.Statement.RaiseErrorOnNotFound = true // 提升ErrorOnNotFound
	tx.Statement.Dest = dest
	tx.callbacks.Query().Execute(tx)
	return
}

// Find find records that match given conditions
func (db *DB) Find(dest interface{}, conds ...interface{}) (tx *DB) {
	tx = db.getInstance()
	if len(conds) > 0 {
		tx.Statement.AddClause(clause.Where{Exprs: tx.Statement.BuildCondition(conds[0], conds[1:]...)})
	}
    // 没有提升ErrorOnNotFound
	tx.Statement.Dest = dest
	tx.callbacks.Query().Execute(tx)
	return
}

-- scan.go
func Scan(rows *sql.Rows, db *DB, initialized bool) {
	columns, _ := rows.Columns()
	values := make([]interface{}, len(columns))
	db.RowsAffected = 0
    // ...
	if db.RowsAffected == 0 && db.Statement.RaiseErrorOnNotFound {
		db.AddError(ErrRecordNotFound)
	}
}
```

# 参考
1.[GORM最佳实践之ErrRecordNotFound的坑](https://jingwei.link/2018/11/18/gorm-err-record-not-found.html#gorm%E4%BD%95%E6%97%B6%E6%8A%9B%E5%87%BAerrrecordnotfound%E9%94%99%E8%AF%AF)
