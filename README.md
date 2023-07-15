# Search for openjdk mailing lists

This project allows you to search across several [OpenJDK mailing lists](https://mail.openjdk.org/mailman/listinfo). 

The unique thing of this project is local search. When you open [a search page](https://hixon10.github.io/openjdk-mailing-lists-search/), `js-script` downloads `SQLite database` with indexed mailing lists.

The bad thing is you need to download the whole search index. The good thing is you can execute arbitrary sql queries on top of this db.

## Currently indexed mailing lists:
- [https://mail.openjdk.org/pipermail/valhalla-dev/](https://mail.openjdk.org/pipermail/valhalla-dev/)
- [https://mail.openjdk.org/pipermail/amber-dev](https://mail.openjdk.org/pipermail/amber-dev)
- [https://mail.openjdk.org/pipermail/panama-dev](https://mail.openjdk.org/pipermail/panama-dev)
- [https://mail.openjdk.org/pipermail/loom-dev](https://mail.openjdk.org/pipermail/loom-dev)
- [https://mail.openjdk.org/pipermail/lilliput-dev](https://mail.openjdk.org/pipermail/lilliput-dev)
- [https://mail.openjdk.org/pipermail/leyden-dev](https://mail.openjdk.org/pipermail/leyden-dev)
- [https://mail.openjdk.org/pipermail/jdk-dev](https://mail.openjdk.org/pipermail/jdk-dev)
- [https://mail.openjdk.org/pipermail/graal-dev](https://mail.openjdk.org/pipermail/graal-dev)
- [https://mail.openjdk.org/pipermail/announce](https://mail.openjdk.org/pipermail/announce)
 

## How it works
1. Periodically, [github action](https://github.com/Hixon10/openjdk-mailing-lists-search/blob/main/.github/workflows/reindexer.yaml) runs an [indexer](https://github.com/Hixon10/openjdk-mailing-lists-search/tree/main/indexer). The indexer downloads new emails from an archive (e.g., [The amber-dev Archives](https://mail.openjdk.org/pipermail/amber-dev/)), and inserts them to a [db-part-0*](https://github.com/Hixon10/openjdk-mailing-lists-search/tree/main/docs).
2. When you open a [search frontend](https://hixon10.github.io/openjdk-mailing-lists-search/), [sql.js](https://github.com/sql-js/sql.js/) lib downloads database parts `db-part-0*`, and prepares the db, using `WebAssembly`.
3. Finally, you can execute any SQL-queries locally, without any server interaction.


## Monitoring implementation details
1. After reindexing, we check a new database size. If it is differ from the old one, we write new database size and current timestamp to [dbsize.txt](https://github.com/Hixon10/openjdk-mailing-lists-search/blob/main/docs/dbsize.txt). It is implemented as one of [actions steps](https://github.com/Hixon10/openjdk-mailing-lists-search/blob/main/.github/workflows/reindexer.yaml#L26).
2. We have a [Cloudflare Worker](https://github.com/Hixon10/openjdk-mailing-lists-search/blob/main/monitoring/worker.js). It has [Cron trigger](https://developers.cloudflare.com/workers/platform/cron-triggers/), which executes this worker every few hours.
3. The worker checks, when the database was updated last time. If it happend more than [5 days](https://github.com/Hixon10/openjdk-mailing-lists-search/blob/main/monitoring/worker.js#L15) ago, the worker sends email.

## Credits
1. [Gunnar Morling - How I Built a Serverless Search for My Blog](https://www.morling.dev/blog/how-i-built-a-serverless-search-for-my-blog/) for idea of this project. 
2. [https://github.com/sql-js/sql.js/](https://github.com/sql-js/sql.js/) for a great library, which allows to use SQLite from browser.
3. [https://github.com/](https://github.com/) for free Actions for open-source projects.