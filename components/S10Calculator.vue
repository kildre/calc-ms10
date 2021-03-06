<template>
  <div>
    <b-jumbotron
      header="MicroEssentials S10 Value Calculator"
      lead="Built with Bootstrap v4 Components for Vue.js 2"
    >
    </b-jumbotron>
    <div class="main row">
      <div class="col-sm-6 left-col">
        <b-form>
          <b-card bg-variant="light">
            <b-form-group label="Select Crop">
              <b-form-select @change="setCrop" :options="getCrops">
                <template #first>
                  <b-form-select-option :value="null" disabled
                    >-- Select --</b-form-select-option
                  >
                </template>
              </b-form-select>
            </b-form-group>
          </b-card>

          <b-card bg-variant="light">
            <b-form-group label="Crop Price">
              <b-input-group prepend="$">
                <b-form-input
                  type="number"
                  v-model="cropPrice"
                  step="0.01"
                  min="0.00"
                ></b-form-input>
              </b-input-group>
            </b-form-group>
          </b-card>

          <b-card bg-variant="light">
            <b-form-group label="Current Blend">
              <b-form-select v-model="currentBlend" :options="getFertilizers">
                <template #first>
                  <b-form-select-option :value="null" disabled
                    >-- Select --</b-form-select-option
                  >
                </template>
              </b-form-select>
            </b-form-group>
          </b-card>

          <b-card bg-variant="light">
            <b-form-group label="Do you Balance for Nitrogen?">
              <b-form-checkbox v-model="useUrea"> </b-form-checkbox>
            </b-form-group>
          </b-card>

          <b-card bg-variant="light">
            <b-form-group label="Include Sulfur in the blend?">
              <b-form-checkbox v-model="includeSulfurInBlend">
              </b-form-checkbox>
            </b-form-group>
            <b-form-group
              label="Select Sulfur Source"
              v-if="includeSulfurInBlend"
            >
              <b-form-select v-model="sulfur" :options="getSulfurs">
                <template #first>
                  <b-form-select-option :value="null" disabled
                    >-- Select --</b-form-select-option
                  >
                </template>
              </b-form-select>
            </b-form-group>
          </b-card>

          <b-card bg-variant="light">
            <b-form-group label="Blend Rate of P2O5">
              <b-input-group append="LB/Acre">
                <b-form-input
                  type="number"
                  v-model="phosphorousOxide"
                  step="0.01"
                  min="0.00"
                ></b-form-input>
              </b-input-group>
            </b-form-group>
          </b-card>

          <b-card bg-variant="light">
            <b-form-group label="Expected Yield Advantage">
              <b-input-group append="BU/Acre">
                <b-form-input
                  type="number"
                  v-model="yieldAdvantage"
                  step="0.01"
                  min="0.00"
                ></b-form-input>
              </b-input-group>
            </b-form-group>
          </b-card>

          <b-card bg-variant="light">
            <b-form-group label="Local Retail Prices" label-size="lg">
              <b-form-group label="MicroEssentials">
                <b-input-group prepend="$" append="/ Ton">
                  <b-form-input
                    type="number"
                    v-model="microEssentialsPricePerTon"
                    step="0.01"
                    min="0.00"
                  ></b-form-input>
                </b-input-group>
              </b-form-group>
              <b-form-group label="DAP">
                <b-input-group prepend="$" append="/ Ton">
                  <b-form-input
                    type="number"
                    v-model="dapPricePerTon"
                    step="0.01"
                    min="0.00"
                  ></b-form-input>
                </b-input-group>
              </b-form-group>
              <b-form-group label="MAP">
                <b-input-group prepend="$" append="/ Ton">
                  <b-form-input
                    type="number"
                    v-model="mapPricePerTon"
                    step="0.01"
                    min="0.00"
                  ></b-form-input>
                </b-input-group>
              </b-form-group>
              <b-form-group label="Urea" v-if="useUrea">
                <b-input-group prepend="$" append="/ Ton">
                  <b-form-input
                    type="number"
                    v-model="ureaPricePerTon"
                    step="0.01"
                    min="0.00"
                  ></b-form-input>
                </b-input-group>
              </b-form-group>
              <b-form-group label="Ammonium Sulfate (AMS) / Ton">
                <b-input-group prepend="$" append="/ Ton">
                  <b-form-input
                    type="number"
                    v-model="amsPricePerTon"
                    step="0.01"
                    min="0.00"
                  ></b-form-input>
                </b-input-group>
              </b-form-group>
              <b-form-group label="Elemental Sulfur (0-0-0-90S)">
                <b-input-group prepend="$" append="/ Ton">
                  <b-form-input
                    type="number"
                    v-model="esPricePerTon"
                    step="0.01"
                    min="0.00"
                  ></b-form-input>
                </b-input-group>
              </b-form-group>
            </b-form-group>
          </b-card>
        </b-form>
      </div>
      <div class="col-sm-6 right-col">
        <h2>Net Return Per Acre</h2>
        <h3>${{ getNetReturnPerAcre }}</h3>
      </div>
    </div>
  </div>
</template>

<script>
import Logo from "~/components/Logo";

import { CROPS } from "~/enums/crops";
import { PRODUCTS } from "~/enums/products";

export default {
  components: {
    Logo
  },
  data() {
    return {
      currentBlend: null,
      includeSulfurInBlend: null,
      sulfur: null,
      phosphorousOxide: null,
      useUrea: null,
      cropPrice: null,
      yieldAdvantage: null,
      microEssentialsPricePerTon: null,
      dapPricePerTon: null,
      mapPricePerTon: null,
      amsPricePerTon: null,
      esPricePerTon: null,
      ureaPricePerTon: null
    };
  },
  computed: {
    getNetReturnPerAcre() {
      if (this.yieldAdvantage == null || this.yieldAdvantage == 0) {
        return 0;
      } else {
        return (
          this.cropPrice * this.yieldAdvantage -
          (this.getCostPerAcreForMicroEssentials - this.getCostPerAcreForSulfur)
        ).toFixed(2);
      }
    },
    getCrops() {
      return CROPS.map(crop => {
        return { value: crop, text: crop.name };
      });
    },
    getFertilizers() {
      return PRODUCTS.fertilizers
        .filter(fertilizer => fertilizer.compare)
        .map(fertilizer => {
          return { value: fertilizer, text: fertilizer.name };
        });
    },
    getSulfurs() {
      return PRODUCTS.sulfurs.map(sulfur => {
        return { value: sulfur, text: sulfur.name };
      });
    },
    getAms() {
      return PRODUCTS.sulfurs.find(sulfur => sulfur.name == "AMS");
    },
    getElementalSulfur() {
      return PRODUCTS.sulfurs.find(
        sulfur => sulfur.name == "Elemental Sulfur 90%"
      );
    },
    getDap() {
      return PRODUCTS.fertilizers.find(fertilizer => fertilizer.name == "DAP");
    },
    getMap() {
      return PRODUCTS.fertilizers.find(fertilizer => fertilizer.name == "MAP");
    },
    getMicroEssentials() {
      return PRODUCTS.fertilizers.find(
        fertilizer => fertilizer.name == "MicroEssentials S10"
      );
    },
    getCostPerAcreForMicroEssentials() {
      return (
        this.getTotalCostForMicroEssentials +
        this.getTotalCostForUreaForMicroEssentials
      );
    },
    getCostPerAcreForSulfur() {
      if (this.currentBlend) {
        if (this.currentBlend.name == "DAP") {
          return (
            this.getTotalCostForDap +
            this.getTotalCostForAmsForDap +
            this.getTotalCostForTiger90ForMapOrDap
          );
        } else if (this.currentBlend.name == "MAP") {
          return (
            this.getTotalCostForMap +
            this.getTotalCostForUreaForMap +
            this.getTotalCostForAmsForMap +
            this.getTotalCostForTiger90ForMapOrDap
          );
        }
      }

      return 0;
    },
    getUrea() {
      return PRODUCTS.nitrogens.find(nitrogen => nitrogen.name == "Urea");
    },
    getUreaCostForMicroEssentials() {
      if (!this.useUrea) {
        return 0;
      } else {
        return (
          (this.getNitrogenFromUreaForMicroEssentials /
            this.getUrea.nitrogen /
            2000) *
          this.ureaPricePerTon
        );
      }
    },
    getNitrogenFromUreaForMicroEssentials() {
      if (!this.useUrea) {
        return 0;
      } else {
        if (this.currentBlend) {
          if (this.currentBlend.name == "DAP") {
            return (
              this.getTotalNitrogenForDap -
              this.getNitrogenFromPhosphateForMicroEssentials
            );
          } else if (this.currentBlend.name == "MAP") {
            return (
              this.getTotalNitrogenForMap -
              this.getNitrogenFromPhosphateForMicroEssentials
            );
          }
        }

        return 0;
      }
    },
    getNitrogenFromUreaForMap() {
      return 0;
    },
    getTotalNitrogenForMicroEssentials() {
      return (
        this.getNitrogenFromPhosphateForMicroEssentials +
        this.getNitrogenFromUreaForMicroEssentials
      );
    },
    getTotalNitrogenForDap() {
      return (
        this.getNitrogenFromPhosphateForDap + this.getNitrogenFromAmsForDap
      );
    },
    getTotalNitrogenForMap() {
      return (
        this.getNitrogenFromPhosphateForMap +
        this.getNitrogenFromUreaForMap +
        this.getNitrogenFromAmsForMap
      );
    },
    getNitrogenFromPhosphateForMicroEssentials() {
      return (
        this.getPoundsPerAcreAppliedForMicroEssentials *
        this.getMicroEssentials.nitrogen
      );
    },
    getNitrogenFromPhosphateForDap() {
      return this.getPoundsPerAcreAppliedForDap * this.getDap.nitrogen;
    },
    getNitrogenFromPhosphateForMap() {
      return this.getPoundsPerAcreAppliedForMap * this.getMap.nitrogen;
    },
    getPoundsPerAcreAppliedForMicroEssentials() {
      return this.phosphorousOxide / this.getMicroEssentials.phosphorous;
    },
    getPoundsPerAcreAppliedForDap() {
      return this.phosphorousOxide / this.getDap.phosphorous;
    },
    getPoundsPerAcreAppliedForMap() {
      return this.phosphorousOxide / this.getMap.phosphorous;
    },
    getPoundsPerAcreAppliedForAmsForDap() {
      return this.getSulfateFromAmsForMapOrDap / this.getAms.nitrogen;
    },
    getPoundsPerAcreAppliedForAmsForMap() {
      return this.getSulfateFromAmsForMapOrDap / this.getAms.nitrogen;
    },
    getPoundsPerAcreAppliedForUreaForMicroEssentials() {
      return this.getNitrogenFromUreaForMicroEssentials / this.getUrea.nitrogen;
    },
    getPoundsPerAcreAppliedForUreaForMap() {
      return this.getNitrogenFromUreaForMap / this.getUrea.nitrogen;
    },
    getPoundsPerAcreAppliedForTiger90ForMapOrDap() {
      return (
        this.getElementalSulfurFromTiger90ForMapOrDap /
        this.getElementalSulfur.elementalSulfur
      );
    },
    getSulfateFromAmsForMapOrDap() {
      if (!this.includeSulfurInBlend) {
        return 0;
      } else {
        if (this.sulfur) {
          if (this.sulfur.name == "AMS") {
            return (
              this.getPoundsPerAcreAppliedForMicroEssentials *
                this.getMicroEssentials.sulfate +
              this.getPoundsPerAcreAppliedForMicroEssentials *
                this.getMicroEssentials.elementalSulfur
            );
          } else if (this.sulfur.name == "Elemental Sulfur 90%") {
            return 0;
          } else if (this.sulfur.name == "AMS + ES 50/50") {
            return (
              this.getPoundsPerAcreAppliedForMicroEssentials *
              this.getMicroEssentials.sulfate
            );
          }
        }

        return 0;
      }
    },
    getNitrogenFromAmsForDap() {
      return this.getPoundsPerAcreAppliedForAmsForDap * this.getAms.sulfate;
    },
    getNitrogenFromAmsForMap() {
      return this.getPoundsPerAcreAppliedForAmsForMap * this.getAms.sulfate;
    },
    getTotalCostForMicroEssentials() {
      return (
        (this.getPoundsPerAcreAppliedForMicroEssentials / 2000) *
        this.microEssentialsPricePerTon
      );
    },
    getTotalCostForDap() {
      return (this.getPoundsPerAcreAppliedForDap / 2000) * this.dapPricePerTon;
    },
    getTotalCostForMap() {
      return (this.getPoundsPerAcreAppliedForMap / 2000) * this.mapPricePerTon;
    },
    getTotalCostForUreaForMicroEssentials() {
      return (
        (this.getPoundsPerAcreAppliedForUreaForMicroEssentials / 2000) *
        this.ureaPricePerTon
      );
    },
    getTotalCostForUreaForMap() {
      return (
        (this.getPoundsPerAcreAppliedForUreaForMap / 2000) *
        this.ureaPricePerTon
      );
    },
    getTotalCostForAmsForDap() {
      return (
        (this.getPoundsPerAcreAppliedForAmsForDap / 2000) * this.amsPricePerTon
      );
    },
    getTotalCostForAmsForMap() {
      return (
        (this.getPoundsPerAcreAppliedForAmsForMap / 2000) * this.amsPricePerTon
      );
    },
    getTotalCostForTiger90ForMapOrDap() {
      return (
        (this.getPoundsPerAcreAppliedForTiger90ForMapOrDap / 2000) *
        this.esPricePerTon
      );
    },
    getTotalSulfateSulfurForMicroEssentials() {
      return (
        this.getPoundsPerAcreAppliedForMicroEssentials *
        this.getMicroEssentials.sulfate
      );
    },
    getTotalElementalSulfureForMicroEssentials() {
      return (
        this.getPoundsPerAcreAppliedForMicroEssentials *
        this.getMicroEssentials.elementalSulfur
      );
    },
    getElementalSulfurFromTiger90ForMapOrDap() {
      if (!this.includeSulfurInBlend) {
        return 0;
      } else {
        if (this.sulfur) {
          if (this.sulfur.name == "AMS") {
            return 0;
          } else if (this.sulfur.name == "Elemental Sulfur 90%") {
            return (
              this.getTotalSulfateSulfurForMicroEssentials +
              this.getTotalElementalSulfureForMicroEssentials
            );
          } else if (this.sulfur.name == "AMS + ES 50/50") {
            return this.getTotalElementalSulfureForMicroEssentials;
          } else {
            return 0;
          }
        }

        return 0;
      }
    }
  },
  methods: {
    setCrop(crop) {
      if (crop) {
        this.yieldAdvantage = crop.yieldAdvantage;
        this.cropPrice = crop.currentPrice;
      }
    }
  }
};
</script>

<style scoped>
.main.row {
  margin-top: 30px;
}
.card:not(:last-child) {
  margin-bottom: 15px;
}
</style>
