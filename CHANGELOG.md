# Changelog for v2.2

## Highlights

This release adds many improvements to Ecto with a handful of bug fixes. We recommend reading the full list of enhancements below. Here are some of the highlights.

Ecto now supports specifying fields sources. This is useful when you use a database with non-conventional names, such as uppercase letters or names with hyphens:

    field :name, :string, source: :NAME
    field :user_name, :string, source: :"user-name"

On the migrations side, `execute/2` function was added, which allows developers to describe up and down commands inside the `change` callback:

    def change do
      execute "CREATE EXTENSION citext",
              "DROP EXTENSION citext"
    end

The `ecto.migrate` and `ecto.rollback` tasks have also been enhanced with the `--log-sql` option which is helpful when debugging errors or during deployments to keep track of your production database changes.

Ecto now will also warn at compile time of invalid relationships, such as a belongs_to that points to a schema that does not exist.

The query syntax also seen some improvements: map updates are supported in subqueries, the new `Ecto.Query.select_merge/3` makes it easier to write composable `select` clauses in queries and the `type/2` macro has been extended to support casting of fragments and fields in schemaless queries.

Finally, the UPSERT support added on Ecto v2.1 is getting more improvements: the `{:constraint, constraint}` is now supported as conflict target and the `:returning` option was added to `Ecto.Repo.insert/2`, mirroring the behaviour of `insert_all`.

## v2.2.0-rc.0 (2017-08-08)

### Enhancements

  * [Ecto.Adapters] Accept IO data from adapters to reduce memory usage when performing large queries
  * [Ecto.Adapters.SQL] Also add `Ecto.Repo.to_sql/2` to Ecto.Repo based on SQL adapters
  * [Ecto.Adapters.Postgres] Use the "postgres" database for create/drop database commands
  * [Ecto.Adapters.MySQL] Use TCP connections instead of MySQL command client to create & drop database
  * [Ecto.Changeset] Support `action: :ignore` in changeset which is useful when casting associations and embeds and one or more children need to be rejected/ignored under certain circumstances
  * [Ecto.Changeset] Add `:repo_opts` field to `Ecto.Changeset` which are given as options to to the repository whenever an operation is performed
  * [Ecto.Changeset] Add `apply_action/2`
  * [Ecto.Changeset] Add prefix constraint name checking to constraint validations
  * [Ecto.Changeset] Allow assocs and embeds in `change/2` and `put_change/3` - this gives a more generic API for developers to work that does not require explicit knowledge of the field type
  * [Ecto.Migration] Add reversible `execute/2` to migrations
  * [Ecto.Migration] Add `:migration_timestamps` and `:migration_primary_key` to control the migration defaults from the repository
  * [Ecto.Migrator] Allow migration/rollback to log SQL commands via the `--log-sql` flag
  * [Ecto.LogEntry] Add `:caller_pid` to the Ecto.LogEntry struct
  * [Ecto.Query] Allow map updates in subqueries
  * [Ecto.Query] Support fragment and field access in `type/2` in select
  * [Ecto.Query] Add `select_merge/3` as a composable API for selects
  * [Ecto.Repo] Implement `:returning` option on insert
  * [Ecto.Repo] Add ON CONSTRAINT support to `:conflict_target` on `insert` and `insert_all`
  * [Ecto.Repo] Raise `MultiplePrimaryKeyError` when primary key is not unique on DB side
  * [Ecto.Schema] Validate schemas after compilation - this helps developers catch early mistakes such as foreign key mismatches early on
  * [Ecto.Type] Allow adapters to pass Date, Time, NaiveDateTime and DateTime on load if desired
  * [Ecto.UUID] Allow casting binary UUIDs
  * [mix ecto.drop] Add `--force`
  * [mix ecto.load] Add `--force` and prompt user to confirm before continuing in production

### Bug fixes

  * [Ecto.Changeset] Remove the field from changes if it does not pass `validate_required`
  * [Ecto.Changeset] Raise if changeset struct does not match relation
  * [Ecto.Query] Consistently raise if `nil` is interpolated on the right side of `in`
  * [Ecto.Query] Properly expand macros in `select`
  * [Ecto.Query] Support `or_having` in keyword query
  * [Ecto.Query] Properly count the parameters when using interpolation inside a `select` inside a `subquery`
  * [Ecto.Query] Properly `select` expressions in subquery (for example, `{p.field}` doesn't make sense as a subquery return but it was silently accepted in the past)
  * [Ecto.Repo] Set struct prefix on `insert`, `delete`, and `update` when the prefix is given as an option
  * [Ecto.UUID] Validate UUID version on casting
  * [mix ecto.*] Make sure `Logger` is rebootted when running ecto tasks
  * [mix ecto.*] No longer mark tasks as recursive and instead collect all repositories upfront. This fixes a bug where migration and rollback commands for a given repository could be executed multiple times from an umbrella project

### Deprecations

  * [Ecto.DateTime] `Ecto.DateTime` as well as `Ecto.Date` and `Ecto.Time` are deprecated in favor of `:naive_datetime`, `:date` and `:time` respectively
  * [Ecto.Repo] Using `{:system, env}` to configure the repository URL is deprecated in favor of a custom init/2 callback

## Previous versions

  * See the CHANGELOG.md [in the v2.1 branch](https://github.com/elixir-ecto/ecto/blob/v2.1/CHANGELOG.md)
