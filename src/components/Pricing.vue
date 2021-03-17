<template>
  <div class="ui segment" id="main">
    <div class="ui grid left aligned">
      <div class="ui column nine wide form horizontal">
        <p>Let’s estimate your reporting cost:</p>

        <strong>How many campaigns do you expect to run this year?</strong>
        <div class="fields">
          <!-- nothing -->
          <div class="field" style="width: calc(100% - 60px)">
            <div class="ui slider" id="slider-1"></div>
          </div>
          <div class="field" style="width: 60px">
            <div class="ui input">
              <input
                type="text"
                id="slider-input-1"
                v-model="campaignsPerMonth"
              />
            </div>
          </div>
        </div>

        <strong>How many influencers on each campaign?</strong>
        <div class="fields">
          <div class="field" style="width: calc(100% - 60px)">
            <div class="ui slider" id="slider-2"></div>
          </div>
          <div class="field" style="width: 60px">
            <div class="ui input">
              <input
                type="text"
                id="slider-input-2"
                v-model="influencersPerCampaign"
              />
            </div>
          </div>
        </div>

        <div class="ui four cards">
          <div
            v-for="model in paymentModels"
            :key="model.reportCost"
            class="ui card"
            :class="{ active: model.reportCost == costPerReport }"
            @click="setCostPerReport(model.reportCost)"
          >
            <div class="content center aligned">
              Buy
              <span v-if="model.bundleSize == 1">
                {{ totalReports }} reports as you go
              </span>
              <span v-else>
                {{ Math.ceil(totalReports / model.bundleSize) }}
                pack{{
                  Math.ceil(totalReports / model.bundleSize) > 1 ? "s" : ""
                }}
                of
                <br />
                {{ model.name }}
                for ${{ model.cost }}
                <span v-if="Math.ceil(totalReports / model.bundleSize) > 1">
                  each</span
                >
              </span>

              <!-- <input
                type="radio"
                :value="model.reportCost"
                v-model="costPerReport"
              /> -->
            </div>
          </div>
        </div>
      </div>

      <div class="ui column seven wide" id="rightSide">
        <h3 class="ui header">Your reporting cost is:</h3>

        <h3 class="ui header">
          <div class="sub header">Per report cost</div>
          ${{ costPerReport }}/report
          <div
            class="ui label mini white"
            v-if="selectedPaymentModel.bundleDiscount"
          >
            {{ selectedPaymentModel.bundleDiscount }}
          </div>
        </h3>

        <h3 class="ui header">
          <div class="sub header">Estimated monthly total</div>
          ${{ Math.round((12 * 199 + totalCost) / 12).toLocaleString() }}/month
        </h3>

        <h3 class="ui header">
          <div class="sub header">Estimated total cost for one year</div>
          ${{ (12 * 199 + totalCost).toLocaleString() }}/year ({{
            creditsBought
          }}
          reports)
        </h3>

        <div v-if="creditsRemaining > 0">
          {{ creditsRemaining }} report credits left over (credits don't expire)
        </div>
      </div>
    </div>
  </div>
</template>

<script>
const $ = window.jQuery;

export default {
  name: "Pricing",
  data() {
    return {
      paymentModels: {
        29: { name: "Pay as You Go", cost: 29, reportCost: 29, bundleSize: 1 },
        24: {
          name: "50 Credits",
          cost: 1200,
          reportCost: 24,
          bundleSize: 50,
          bundleDiscount: "17% off",
        },
        19: {
          name: "100 Credits",
          cost: 1900,
          reportCost: 19,
          bundleSize: 100,
          bundleDiscount: "34% off",
        },
        15: {
          name: "200 Credits",
          cost: 3000,
          reportCost: 15,
          bundleSize: 200,
          bundleDiscount: "48% off",
        },
      },
      campaignsPerMonth: 5,
      influencersPerCampaign: 5,
      costPerReport: 29,
    };
  },
  computed: {
    selectedPaymentModel() {
      return this.paymentModels[this.costPerReport];
    },
    creditsRemaining() {
      return this.creditsBought - this.totalReports;
    },
    creditsBought() {
      let credits = this.totalReports;
      if (this.costPerReport === 24) {
        credits = 50;
      } else if (this.costPerReport === 19) {
        credits = 100;
      } else if (this.costPerReport === 15) {
        credits = 200;
      }
      let packs = Math.ceil(this.totalReports / credits);
      return packs * credits;
    },
    totalReports() {
      return Math.max(this.campaignsPerMonth * this.influencersPerCampaign, 1);
    },
    totalCost() {
      return this.creditsBought * this.costPerReport;
    },
  },
  methods: {
    setCostPerReport(cost) {
      this.costPerReport = cost;
    },
  },
  watch: {
    totalReports(val) {
      if (val < 30) {
        this.costPerReport = 29;
      } else if (val <= 50 && val > 31) {
        this.costPerReport = 24;
      } else if (val <= 100 && val > 51) {
        this.costPerReport = 19;
      } else if (val > 101) {
        this.costPerReport = 15;
      }
    },
  },
  props: {
    msg: String,
  },
  mounted() {
    this.$nextTick(function () {
      const $this = this;
      $("#slider-1").slider({
        min: 0,
        max: 100,
        start: 5,
        step: 5,
        smooth: true,
        onChange: function (value) {
          $this.campaignsPerMonth = value;
        },
        onMove: function (value) {
          $this.campaignsPerMonth = value;
        },
      });

      $("#slider-2").slider({
        min: 0,
        max: 50,
        start: 5,
        step: 5,
        smooth: true,
        onChange: function (value) {
          $this.influencersPerCampaign = value;
        },
        onMove: function (value) {
          $this.influencersPerCampaign = value;
        },
      });
    });
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
#rightSide {
  background-color: #F0806F;
}

#rightSide * {
  color: #fff;
}

#rightSide h3.ui.header {
  font-weight: 800;
}

#rightSide h3.ui.header .sub.header {
  font-weight: 400;
}

.ui.card {
  cursor: pointer;
  user-select: none;
}

.ui.card.active {
  border: 1px solid #000;
}

.ui.card .content {
  font-weight: 600;
}

.ui.mini.label.white {
  border-radius: 20px;
  color: #F0806F !important;
  background-color: #fff;
  font-weight: 800 !important;
}

h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}

#main {
  border-radius: 10px;
}

#main .column {
  padding-top: 50px;
  padding-bottom: 60px;
}

#main .column:first-child {
  padding-left: 50px;
  padding-right: 50px;
}
#main .column:last-child {
  padding-left: 50px;
  padding-right: 50px;
  border-top-right-radius: 10px;
  border-bottom-right-radius: 10px;
}
</style>
