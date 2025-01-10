# Amazon Managed Grafana

Amazon Managed Grafana 的成本和使用情况可视化功能可让你深入了解各个 AWS 账户、AWS 区域、特定 Grafana 工作区实例，以及管理员、编辑者和查看者的许可成本。

要可视化和分析成本与使用量数据，需要创建自定义的 Athena 视图。

1. 在继续之前，请确保已根据[实施概览][cid-implement]中的第 1 步创建了 CUR，并完成第 2 步中所述的 AWS Conformation 模板部署。
2. 现在，使用以下查询创建一个新的 Amazon Athena [视图][view]。该查询会从组织内所有 AWS 账户中获取 Amazon Managed Grafana 的成本和使用数据：

        ```sql
        CREATE OR REPLACE VIEW "grafana_cost" AS 
        SELECT
                 line_item_usage_type,
                 line_item_resource_id,
                 line_item_operation,
                 line_item_usage_account_id,
                 month,
                 year,
                 "sum"(line_item_usage_amount) AS "Usage",
                 "sum"(line_item_unblended_cost) AS cost
        FROM
                 database.tablename -- 请用你的数据库名和表名替换 database.tablename
        WHERE
                 line_item_product_code = 'AmazonGrafana'
        GROUP BY 
                 1, 2, 3, 4, 5, 6
        ```

完成后，你可以将 Athena 作为数据源，在 Amazon Managed Grafana 或 Amazon QuickSight 中构建可视化仪表板，也可直接对刚创建的 Athena 视图执行 [SQL 查询][sql-query]。

[view]: https://athena-in-action.workshop.aws/30-basics/303-create-view.html
[sql-query]: https://docs.aws.amazon.com/athena/latest/ug/querying-athena-tables.html
[cid-implement]: ../../../guides/cost/cost-visualization/cost.md#implementation