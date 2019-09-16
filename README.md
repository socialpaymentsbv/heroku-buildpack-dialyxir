# heroku-buildpack-dialyxir
Create and cache PLT files during test-compile phase, and run `mix dialyzer` during test phase.

### Usage:

Add

```
  defp deps do
    [
      {:dialyxir,
       only: [:dev, :test],
       runtime: false,
       github: "jeremyjh/dialyxir",
       ref: "a27b0c55401e5953ba85f4ace94dbdaf002cd0f7"}
    ]
```

to your `mix.exs` file and set `plt_file` to `priv/plts/dialyzer.plt`

```
  def project do
    [
      ...
      dialyzer: dialyzer(Mix.env())
    ]
  end
```

```
  defp dialyzer(:test) do
    [
      plt_core_path: "priv/plts",
      plt_file: {:no_warn, "priv/plts/dialyzer.plt"}
    ]
  end

  defp dialyzer(_) do
    []
  end
```

### Explenation:

We decided to use commit ref because on dialyxir rc-6 there was still a bug
that required a lot of config as a workaround.

We put both core plts and project plt file in the `priv/plts` for the test environment.
The buildpack reads those from the cache during `test-compile` phase.

The buildpack runs `MIX_ENV=test mix dialyzer`, so it requires that the project was previously compiled
for the `:test` environment.

We couldn't find a way to clear Heroku's CI cache, so we decided to introduce `DIALYXIR_SKIP_CACHE` config.
When set to `true`. We won't read current value from the cache effectively recreating PLTs.
The PLTs will be normally used in the next run.
