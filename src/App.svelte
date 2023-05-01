<script lang="ts">
  import { onMount } from "svelte";

  let titles: string[] = ["#", "Coin", "Price", "Price Change", "24h Volume"];
  let filteredCoins = [];
  let coins = [];
  let ref = null;

  onMount(() => {
    ref.focus();
  });

  const loadCoins = async () => {
    const res = await fetch(
      "https://api.coingecko.com/api/v3/coins/markets?vs_currency=usd&order=market_cap_desc&per_page=100&page=1&sparkline=false"
    );
    const data = await res.json();
    coins = data;
    filteredCoins = data;
  };

  const searchCoins = (value: string) => {
    filteredCoins = coins.filter(
      (coin) =>
        coin.name.toLowerCase().includes(value.toLowerCase()) ||
        coin.symbol.toLowerCase().includes(value.toLowerCase())
    );
  };

  loadCoins();
</script>

<main>
  <div class="container">
    <div class="row">
      <h1 style="color: white">Coin Market</h1>
      <div class="container">
        <input
          type="text"
          class="form-control bg-dark text-white rounded-0 border-0 my-4"
          placeholder="look for some coin info"
          bind:this={ref}
          on:keyup={({ target: { value } }) => searchCoins(value)}
        />
      </div>
      <table class="table table-dark">
        <thead>
          <tr>
            {#each titles as title}
              <th>{title}</th>
            {/each}
          </tr>
        </thead>
        <tbody>
          {#each filteredCoins as coin, i}
            <tr>
              <td class="text-muted">{i + 1}</td>
              <td>
                <img
                  src={coin.image}
                  alt={coin.name}
                  style="width: 2rem"
                  class="img-fluid me-2"
                />
                <span>
                  {coin.name}
                </span>
                <span class="text-muted ms-2 text-uppercase">{coin.symbol}</span
                >
              </td>
              <td>$ {coin.current_price.toLocaleString()}</td>
              <td
                class={coin.price_change_percentage_24h > 0
                  ? "text-success"
                  : "text-danger"}>{coin.price_change_percentage_24h} %</td
              >
              <td>${coin.total_volume.toLocaleString()}</td>
            </tr>
          {/each}
        </tbody>
      </table>
    </div>
  </div>
</main>

<style>
</style>
