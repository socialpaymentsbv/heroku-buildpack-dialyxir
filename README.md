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

to your `mix.exs` file and set `plt_file` to $CACHE_DIR that is set by this buildpack.

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
    [plt_file: {:no_warn, "#{System.get_env("CACHE_PATH")}/dialyzer.plt"}]
  end

  defp dialyzer(_) do
    []
  end
```
