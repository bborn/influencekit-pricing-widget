<template>
  <div class="ui segment compact" id="main">
    <div class="ui large basic label">
      <i
        @click.stop.prevent="decrementcampaignsPerYear"
        class="icon minus link compact fitted"
      ></i>
      <input type="text" v-model="campaignsPerYear" size="2" />
      <i
        @click.stop.prevent="incrementcampaignsPerYear"
        class="icon plus link compact fitted"
      ></i>
      Campaigns this year
    </div>

    <span class="margin-x-small">with</span>

    <div class="ui large basic label">
      <i
        @click.stop.prevent="decrementInfluencersPerCampaign"
        class="icon minus link compact fitted"
      ></i>

      <input type="text" v-model="influencersPerCampaign" size="2" />

      <i
        @click.stop.prevent="incrementInfluencersPerCampaign"
        class="icon plus link compact fitted"
      ></i>
      Influencers
    </div>

    <strong class="margin-x-small">
      <i class="icon equals"></i>
    </strong>

    <div class="ui large basic label result">
      ${{ Math.round((12 * 199 + totalCost) / 12).toLocaleString() }}/month

      <span class="text-light">
        <!-- or ${{ costPerReport }}/report -->

        <span id="discount-label" v-if="selectedPaymentModel.bundleDiscount" class="margin-x-small">
          Save {{ selectedPaymentModel.bundleDiscount }} per report
        </span>
      </span>
    </div>

    <div class="ui grid left aligned" style="display: none">
      <div class="ui column nine wide form horizontal">
        <div class="body1-bold pb-8 black-100">
          How many campaigns do you expect to run this year?
        </div>
        <div class="fields">
          <div class="field" style="width: calc(100% - 75px)">
            <div class="ui slider" id="slider-1"></div>
          </div>
          <div class="field" style="width: 75px">
            <div class="ui input">
              <input
                type="text"
                id="slider-input-1"
                v-model="campaignsPerYear"
              />
            </div>
          </div>
        </div>
        <div class="body1-bold pt-32 pb-8 black-100">
          How many influencers on each campaign?
        </div>
        <div class="fields">
          <div class="field" style="width: calc(100% - 75px)">
            <div class="ui slider" id="slider-2"></div>
          </div>
          <div class="field" style="width: 75px">
            <div class="ui input">
              <input
                type="text"
                id="slider-input-2"
                v-model="influencersPerCampaign"
              />
            </div>
          </div>
        </div>
        <div class="ui four cards pt-40" style="display: none">
          <div
            v-for="model in paymentModels"
            :key="model.reportCost"
            class="ui card"
            :class="{ active: model.reportCost == costPerReport }"
            @click="setCostPerReport(model.reportCost)"
          >
            <div class="content center aligned body2-semibold">
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
            </div>
          </div>
        </div>
      </div>
      <div class="ui column seven wide" id="rightSide">
        <h3 class="body1-bold white-100 pb-32">Your reporting cost is:</h3>
        <h3 class="h4-extrabold white">
          <div class="body1-bold white-100 pb-12">As low as:</div>
          ${{ costPerReport }}/report
          <div id="discount-label" v-if="selectedPaymentModel.bundleDiscount">
            {{ selectedPaymentModel.bundleDiscount }}
          </div>
        </h3>
        <h3 class="h4-extrabold white">
          <div class="body1-bold white-100 pb-12 pt-32">
            Estimated monthly total:
          </div>
          ${{ Math.round((12 * 199 + totalCost) / 12).toLocaleString() }}/month
          <!-- <br>
          2. ${{ Math.round((12 * 199 + (creditsBought * costPerReport)) / 12).toLocaleString() }}/month -->
        </h3>
        <h3 class="h4-extrabold white" style="display: none">
          <div class="body1-bold white-100 pb-12 pt-32">
            Estimated total cost for one year
          </div>
          ${{ (12 * 199 + totalCost).toLocaleString() }}/year ({{
            creditsBought
          }}
          reports)
        </h3>
        <!-- <div v-if="creditsRemaining > 0" class="pt-8">
          {{ creditsRemaining }} report credits left over (credits don't expire)
        </div> -->
        <div class="pt-48 pb-32" style="display: none">
          <ul>
            <li>Unlimited Users</li>
            <li>Influencer Messaging</li>
            <li>Influencer Hub</li>
            <li>Unlimited File Sharing</li>
            <li>
              Advanced Reporting
              <p style="padding-left: 8px">
                Report Builder is an advanced report building tool that lets you
                aggregate multiple InfluenceKit reports together into one
              </p>
            </li>
          </ul>
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
          bundleDiscount: "17%",
        },
        19: {
          name: "100 Credits",
          cost: 1900,
          reportCost: 19,
          bundleSize: 100,
          bundleDiscount: "34%",
        },
        15: {
          name: "200 Credits",
          cost: 3000,
          reportCost: 15,
          bundleSize: 200,
          bundleDiscount: "48%",
        },
      },
      campaignsPerYear: 4,
      influencersPerCampaign: 6,
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
      return Math.max(this.campaignsPerYear * this.influencersPerCampaign, 1);
    },
    totalCost() {
      // return this.creditsBought * this.costPerReport;
      return this.totalReports * this.costPerReport;
    },
  },
  methods: {
    incrementcampaignsPerYear() {
      this.campaignsPerYear++;
    },
    decrementcampaignsPerYear() {
      if (this.campaignsPerYear > 1) {
        this.campaignsPerYear--;
      }
    },
    incrementInfluencersPerCampaign() {
      this.influencersPerCampaign++;
    },
    decrementInfluencersPerCampaign() {
      if (this.influencersPerCampaign > 1) {
        this.influencersPerCampaign--;
      }
    },
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
        max: 30,
        start: 5,
        step: 1,
        smooth: true,
        onChange: function (value) {
          $this.campaignsPerYear = value;
        },
        onMove: function (value) {
          $this.campaignsPerYear = value;
        },
      });

      $("#slider-2").slider({
        min: 0,
        max: 50,
        start: 5,
        step: 1,
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


<style scoped>
.ui.label.large.basic {
  min-width: 300px;
  font-size: 18px;
  user-select: none;
  padding: 18px;
  border-radius: 6px;
}

.ui.label.large.basic i.icon {
  font-size: 14px;
  margin-right: 9px !important;
  margin-left: 9px !important;
  padding-left: 7px;
  padding-right: 7px;
}

.ui.label.result {
  min-width: 350px !important;
}

.ui.label.large input {
  border: none;
  font-weight: 600;
  display: inline-block;
  text-align: center;
}

#main {
  line-height: 4em;
  text-align: center;
  font-size: 18px;
  border-radius: 6px;
  border-width: 0;
  background-color: #F1F8F6;
  box-shadow: none;
}

#discount-label {
  vertical-align: middle;
  font-family: "Inter", sans-serif;
  font-weight: 500;
  font-size: 12px;
  line-height: 12px;
  border: none;
  text-align: center;
  padding: 4px 8px;
  display: inline-block;
  border-radius: 99px;
  background-color: rgba(0, 0, 0, 0.87);
  color: #fff;
}

.margin-x-small {
  margin-left: 16px;
  margin-right: 16px;
}

.text-light {
  font-weight: normal !important;
}
</style>