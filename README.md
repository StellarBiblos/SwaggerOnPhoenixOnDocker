# SwaggerOnPhoenixOnDocker

Swagger-UI + Elixir + PhoenixをDockerで使うためのリポジトリ

[elixir-on-docker](https://github.com/ponday-dev/elixir-on-docker)のパクリ

# 使い方

まずはイメージをビルド

```sh
docker-compose build
```

プロジェクトを作成する

```sh
docker-compose run app mix phx.new . --app <app_name> --database mysql
```

phoenix_swaggar用のconfigを追記する

```yaml
# config/config.exs or config/dev.exs
config :phoenix_sample, :phoenix_swagger,
  swagger_files: %{
    "priv/swagger/swagger.json" => [
      router: PhoenixSample.Router,
      endpoint: PhoenixSample.Endpoint
    ]
  }

config :phoenix_sample, PhoenixSample.Endpoint,
  url: [host: "localhost"]
```

mix.exsにdeps追加

```py
defp deps do
  [
    {:phoenix_swagger, "~> 0.8"}
  ]
end
```

```sh
docker-compose run app mix deps.get
```

データベースの定義を変更する
作成されたプロジェクトフォルダ内の

+ config/dev.exs
+ config/prod.secret.exs

に定義が書かれているので修正する

```yaml
# Configure your database
config :phoenix_sample, PhoenixSample.Repo,
  adapter: Ecto.Adapters.MySQL,
  username: "mariadb",
  password: "p@ssw0rd",
  database: "phoenixdb",
  hostname: "mariadb",
  port: 3306,
  pool_size: 10,
  charset: "utf8mb4"
```

（検証時点ならprod.secret.exsの編集は不要）

データベースのセットアップ

```sh
docker-compose run app mix ecto.create
```

以下のコマンドでアプリを起動

```sh
docker-compose up
```
