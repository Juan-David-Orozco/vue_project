<template>
  <div>
    <div class="row my-1 p-1 bg-light text-primary">
      <div class="col-3">{{gasto.nombre}}</div>
      <div class="col-3">{{new Intl.NumberFormat("de-DE").format(gasto.monto)}}</div>
      <div class="col-3">{{gasto.tipo}}</div>
    </div>
  </div>
</template>

<script>
export default {
  name: "gastosComponente",
  props: ["gasto", "id", "indice"],
  data: function() {
    return {
      test: "test",
    };
  },
};
</script>

<style>
</style>
