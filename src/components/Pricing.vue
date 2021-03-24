<template>
  <div class="ui segment" id="main">
    <div class="ui grid left aligned">
      <div class="ui column nine wide form horizontal">
        <div class="body1-regular mb-40">
          Let’s estimate your reporting cost:
        </div>
        <div class="body1-bold pb-8 black-100">
          How many campaigns do you expect to run this year?
        </div>
        <div class="fields">
          <div class="field" style="width: calc(100% - 60px)">
            <div class="ui slider" id="slider-1"></div>
          </div>
          <div class="field" style="width: 60px">
            <div class="ui input">
              <input type="text" id="slider-input-1" v-model="campaignsPerMonth" />
            </div>
          </div>
        </div>
        <div class="body1-bold pt-32 pb-8 black-100">
          How many influencers on each campaign?
        </div>
        <div class="fields">
          <div class="field" style="width: calc(100% - 60px)">
            <div class="ui slider" id="slider-2"></div>
          </div>
          <div class="field" style="width: 60px">
            <div class="ui input">
              <input type="text" id="slider-input-2" v-model="influencersPerCampaign" />
            </div>
          </div>
        </div>
        <div class="ui four cards pt-40">
          <div v-for="model in paymentModels" :key="model.reportCost" class="ui card"
            :class="{ active: model.reportCost == costPerReport }" @click="setCostPerReport(model.reportCost)">
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
                  each</span>
              </span>
            </div>
          </div>
        </div>
      </div>
      ​
      <div class="ui column seven wide" id="rightSide">
        <h3 class="body1-bold white-100 pb-32">Your reporting cost is:</h3>
        ​
        <h3 class="h4-extrabold white">
          <div class="body1-bold white-100 pb-12">Per report cost</div>
          ${{ costPerReport }}/report
          <div id="discount-label" v-if="selectedPaymentModel.bundleDiscount">
            {{ selectedPaymentModel.bundleDiscount }}
          </div>
        </h3>
        <h3 class="h4-extrabold white">
          <div class="body1-bold white-100 pb-12 pt-32">
            Estimated monthly total
          </div>
          ${{ Math.round((12 * 199 + totalCost) / 12).toLocaleString() }}/month
        </h3>
        <h3 class="h4-extrabold white">
          <div class="body1-bold white-100 pb-12 pt-32">
            Estimated total cost for one year
          </div>
          ${{ (12 * 199 + totalCost).toLocaleString() }}/year ({{
          creditsBought
          }}
          reports)
        </h3>
        <div v-if="creditsRemaining > 0" class="pt-8">
          {{ creditsRemaining }} report credits left over (credits don't expire)
        </div>
        <div class="pt-48 pb-32">
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


<style scoped>
  #rightSide {
    background-color: #F0806F;
    border-radius: 0px 6px 6px 0px;
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

  .ui.card,
  .ui.cards>.card {
    border-radius: 6px;
    -webkit-box-shadow: 0 0 0 1px rgba(0, 0, 0, 0.22);
  }

  .ui.card.active {
    border: 2px solid #707F87;
    box-shadow: none;
    border-radius: 6px;
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
    margin: 0px 0 0;
  }

  ul {
    list-style-type: none;
    padding: 0;
    margin: 0px;
  }

  li {
    display: block;
    margin: 0px 0px 0px 8px;
  }

  a {
    color: #42b983;
  }

  #main {
    border-radius: 6px;
  }

  #main .column {
    padding-top: 40px;
    padding-bottom: 40px;
  }

  #main .column:first-child {
    padding-left: 40px;
    padding-right: 40px;
  }

  #main .column:last-child {
    padding-left: 40px;
    padding-right: 40px;
    border-top-right-radius: 6px;
    border-bottom-right-radius: 6px;
  }

  .ui.slider .inner .track-fill {
    background-color: #F0806F !important;
  }

  .ui.form .fields {
    margin: 0px -6px 0px -14px;
  }

  .h4-extrabold {
    font-family: "Inter", sans-serif;
    font-weight: 800;
    font-size: 24px;
    letter-spacing: -0.2px;
    line-height: 24px;
  }

  .body1-regular {
    font-family: "Inter", sans-serif;
    font-weight: 400;
    font-size: 18px;
    color: rgba(0, 0, 0, 0.87);
    text-align: left;
    line-height: 24px;
  }

  .body1-bold {
    font-family: "Inter", sans-serif;
    font-weight: 700;
    font-size: 18px;
    text-align: left;
    line-height: 20px;
  }

  .body2-semibold {
    font-family: "Inter", sans-serif;
    font-weight: 600;
    font-size: 14px;
    text-align: center;
    line-height: 16px;
    vertical-align: middle;
  }

  .black-100 {
    color: #000000;
  }

  .black-87 {
    color: rgba(0, 0, 0, 0.87);
  }

  .white-100 {
    color: #FFFFFF;
  }

  .mb-40 {
    margin-bottom: 40px;
  }

  .pt-48 {
    padding-top: 48px;
  }

  .pt-40 {
    padding-top: 40px;
  }

  .pt-32 {
    padding-top: 32px;
  }

  .pb-32 {
    padding-bottom: 32px;
  }

  .pb-12 {
    padding-bottom: 12px;
  }

  .pt-8 {
    padding-top: 8px;
  }

  .pb-8 {
    padding-bottom: 8px;
  }

  .ui.segment {
    box-shadow: none;
    border-radius: 6px;
  }

  li {
    padding-left: -10px;
  }

  li:before {
    content: "\f058";
    /* FontAwesome Unicode */
    font-family: FontAwesome;
    display: inline-block;
    margin-left: -10px;
    width: 18px;
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
    color: #EB554E;
    background-color: #ffffff;
  }

  .ui.input {
    font-family: "Inter", sans-serif;
    font-weight: 600;
    font-size: 16px;
  }
</style>


<style>
  .ui.slider .inner .track-fill {
    background-color: #F0806F !important;
  }
</style>
