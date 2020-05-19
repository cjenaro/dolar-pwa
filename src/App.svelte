<script>
  import { onMount } from "svelte";
  let dolarPromise;
  let toConvert;
  let selectedDolar;
  const USD = "usd";
  const ARS = "ars";
  let selectedCurrency = USD;

  onMount(() => {
    dolarPromise = fetch(
      "https://www.dolarsi.com/api/api.php?type=valoresprincipales"
    ).then(blob => blob.json());
  });

  const selectDolar = dolar => {
    selectedDolar = dolar;
  };

  const selectCurrency = currency => {
    selectedCurrency = currency;
  };

  const avg = (compra, venta) => {
    return ((localeParseFloat(compra) + localeParseFloat(venta)) / 2).toFixed(
      2
    );
  };

  const localeParseFloat = str => {
    if (typeof str === "string") {
      return parseFloat(str.replace(",", "."));
    } else if (typeof str === "number") {
      return str;
    } else {
      return 0;
    }
  };
</script>

<style>
  main {
    text-align: center;
    padding: 1em;
    margin: 0 auto;
    max-width: 675px;
    min-height: calc(100vh - 2em);
    align-items: center;
    display: flex;
    justify-content: center;
    flex-direction: column;
  }

  label {
    color: rebeccapurple;
    font-size: 1.2rem;
    text-transform: uppercase;
    letter-spacing: 4px;
    margin-bottom: 0.6rem;
    width: 100%;
    max-width: 320px;
    text-align: left;
    position: relative;
  }

  label::after {
    content: "$";
    color: rebeccapurple;
    position: absolute;
    left: 1rem;
    bottom: -3rem;
  }

  input {
    width: 100%;
    max-width: 320px;
    border: 1px solid rebeccapurple;
    background-color: transparent;
    margin-bottom: 1rem;
    padding: 1rem 1rem 1rem 2rem;
  }

  ul {
    padding: 0;
    margin: 0;
    list-style: none;
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(320px, 1fr));
    grid-gap: 1rem;
    width: 100%;
    justify-items: center;
  }

  li {
    width: 320px;
    border-radius: 4px 4px 6px 6px;
    background-color: rebeccapurple;
  }

  li button {
    border: 0;
    padding: 0;
    background-color: transparent;
    margin: 0;
    width: 100%;
    height: 100%;
    border-radius: 4px;
  }

  li button.active {
    box-shadow: 2px 2px 0 rebeccapurple;
  }

  .prices {
    display: flex;
    align-items: center;
    flex-wrap: wrap;
    justify-content: space-evenly;
    background-color: whitesmoke;
    border-radius: 0 0 4px 4px;
  }

  .prices > div {
    padding: 1rem;
    flex: 1;
    border-right: 1px solid #66339922;
  }

  .prices > div:last-of-type {
    border-right: 0;
  }

  .name {
    margin: 0;
    width: 100%;
    padding: 1rem 0;
    color: white;
  }

  h6 {
    margin: 0;
  }

  .dolar {
    font-size: 4rem;
    position: relative;
    animation: float 0.7s infinite ease-in-out;
  }

  .dolar-1 {
    animation-delay: 0s;
  }

  .dolar-2 {
    animation-delay: 0.1s;
  }

  .dolar-3 {
    animation-delay: 0.2s;
  }

  .result {
    width: 100%;
    max-width: 320px;
    border-radius: 4px;
    flex-direction: column;
    margin-bottom: 2rem;
    box-shadow: 8px 8px 0 3px rebeccapurple;
  }

  .result > div {
    border-right: 0;
    border-bottom: 1px solid #66339922;
    width: calc(100% - 2rem);
  }

  .result > div:last-of-type {
    border-bottom: 0;
  }

  .how-to,
  .solidario {
    text-align: left;
    width: 100%;
    max-width: 320px;
    line-height: 1.8rem;
    color: #333333dd;
  }

  .currency-from {
    width: 100%;
    max-width: 320px;
    display: flex;
    align-items: center;
    justify-content: center;
    border-radius: 4px;
    margin-bottom: 1rem;
    background: whitesmoke;
  }

  .currency-from button {
    padding: 2rem 0;
    flex: 1;
    margin: 0;
    border: 0;
    font-size: 2rem;
  }

  .currency-from button.selected {
    box-shadow: inset 0 0 4px 4px #00000022;
    outline: 0;
  }

  h1 {
    font-size: 1.4rem;
  }
</style>

<main>
  {#await dolarPromise}
    <p>
      <span class="dolar dolar-1">💸</span>
      <span class="dolar dolar-2">💸</span>
      <span class="dolar dolar-3">💸</span>
    </p>
  {:then value}
    <h4 class="how-to">
      Elegí el dolar que queres usar para calcular e ingresá el monto a
      convertir
    </h4>
    <label for="to-convert">A convertir:</label>
    <input id="to-convert" type="number" bind:value={toConvert} />
    <div class="currency-from">
      <button
        on:click={() => selectCurrency(USD)}
        class={selectedCurrency === USD ? 'selected' : ''}>
        U$D
      </button>
      <button
        on:click={() => selectCurrency(ARS)}
        class={selectedCurrency === ARS ? 'selected' : ''}>
        AR$
      </button>
    </div>
    {#if selectedDolar && toConvert}
      <div class="prices result">
        <div>
          <h6>Compra</h6>
          <h1>
            {selectedCurrency === USD ? (toConvert * localeParseFloat(selectedDolar.compra)).toFixed(2) : (toConvert / localeParseFloat(selectedDolar.compra)).toFixed(2)}
          </h1>
        </div>
        <div>
          <h6>Venta</h6>
          <h1>
            {selectedCurrency === USD ? (toConvert * localeParseFloat(selectedDolar.venta)).toFixed(2) : (toConvert / localeParseFloat(selectedDolar.venta)).toFixed(2)}
          </h1>
        </div>
        <div>
          <h6>Promedio</h6>
          <h1>
            {selectedCurrency === USD ? (toConvert * avg(selectedDolar.compra, selectedDolar.venta)).toFixed(2) : (toConvert / avg(selectedDolar.compra, selectedDolar.venta)).toFixed(2)}
          </h1>
        </div>
      </div>
    {/if}
    <ul>
      {#each Array.from(value || [])
        .map(v => v.casa)
        .concat([
          {
            nombre: 'Dolar Solidario*',
            compra:
              Array.from(value || []).length > 0 &&
              localeParseFloat(value[0].casa.compra) * 1.3,
            venta:
              Array.from(value || []).length > 0 &&
              localeParseFloat(value[0].casa.venta) * 1.3
          }
        ])
        .filter(d => d.nombre
              .toLowerCase()
              .includes(
                'dolar'
              ) && !d.nombre
              .toLowerCase()
              .includes('soja') && d.nombre.toLowerCase() !== 'dolar') as dolar}
        <li>
          <button
            class={selectedDolar && selectedDolar.nombre === dolar.nombre ? 'active' : ''}
            on:click={() => selectDolar(dolar)}>
            <h4 class="name">{dolar.nombre}</h4>
            <div class="prices">
              <div>
                <h6>Compra</h6>
                <h1>{localeParseFloat(dolar.compra).toFixed(2)}</h1>
              </div>
              <div>
                <h6>Venta</h6>
                <h1>{localeParseFloat(dolar.venta).toFixed(2)}</h1>
              </div>
              <div>
                <h6>Promedio</h6>
                <h1>{avg(dolar.compra, dolar.venta)}</h1>
              </div>
            </div>
          </button>
        </li>
      {/each}
    </ul>
    <p class="solidario">
      * Precio del dolar solidario inferido del valor del dolar oficial
    </p>
  {:catch error}
    <pre>{JSON.stringify(error, null, 2)}</pre>
  {/await}
</main>
