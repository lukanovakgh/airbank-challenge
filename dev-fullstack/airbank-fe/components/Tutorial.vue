<template>
  <div class="page-wrapper">
    <div class="title">Transactions</div>
    <div class="filter-layer">
      <label class="filter-item max">
        Search
        <Input
          type="text"
          placeholder="Search by bank, account, reference, category, date, amount, currency"
          class="search-text"
          :value="searchValue"
          @input="handleSearch"
        />
      </label>
      <label class="filter-item middle">
        Bank
        <Select placeholder="No filter applied" class="select-wrapper" @change="handleBank">
          <Option v-for="(bank, index) in banks" :key="index" :value="bank.id">{{ bank.bank }}</Option>
        </Select>
      </label>
      <label class="filter-item middle">
        Account
        <Select placeholder="No filter applied" class="select-wrapper" @change="handleCategory">
          <Option v-for="(bank, index) in banks" :key="index" :value="bank.id">{{ bank.name }}</Option>
        </Select>
      </label>
      <label class="filter-item min">
        Starting month
        <DatePicker
          format="YYYY/MM"
          placeholder="yyyy-mm"
          @change="handleStartDate"
        />
      </label>
      <label class="filter-item min">
        Ending month
        <DatePicker
          format="YYYY/MM"
          placeholder="yyyy-mm"
          @change="handleEndDate"
        />
      </label>
    </div>
    <Table class="transaction-list" :columns="tableColumns" :dataSource="currentTransactions" :pagination="false">
      <template
        slot="reference"
        slot-scope="value"
      >
        <div :class="value ? 'td-amount' : 'td-currency'"> {{ value || 'No reference provided' }} </div>
      </template>
      <template
        slot="category"
        slot-scope="value"
      >
        <span class="category-chip" v-bind:style="[{background: '#' + categories.find((c) => c.id == value)?.color}]">
          {{ categories.find((c) => c.id == value)?.name }}
         </span>
      </template>
      <template
        slot="amount"
        slot-scope="value, record"
      >
        <div class="td-amount" @click="goDetail">{{ record.amount }}<span class="td-currency">{{ record.currency }}</span></div>
      </template>
    </Table>
  </div>
</template>

<script>
  import { Button, Input, Select, DatePicker, Table } from "ant-design-vue";
  import moment from 'moment';
  import axios from "../.nuxt/axios";

  const Option = Select.Option;

  const categoryList = [
    { id: "6ad0e563-7f94-417d-8370-7eca2e52b2cc", name: "Advertising", color: "7048a3" },
    { id: "0c600155-27a0-40ce-9df5-523f2831ff56", name: "Charges/Fees", color: "ffbf84" },
    { id: "f630d7f1-afb9-4713-b51b-265558836753", name: "Check Outflows", color: "ffbf84" },
    { id: "977a3d02-364d-4c88-9856-85cd93a361ef", name: "Company Investments", color: "958e80" },
    { id: "350a5a32-beee-47f3-bb71-3814d4a7082a", name: "Contractors", color: "f6f2ab" },
    { id: "f41d7624-1187-412a-b314-80a6832f81c0", name: "Credit Card", color: "ffbf84" },
    { id: "49b11f13-8eb6-417d-aa02-467d1686c003", name: "Debt Investment", color: "958e80" },
    { id: "b61354fc-c493-46fe-8edd-87da4f503965", name: "Debt Repayment", color: "958e80" },
    { id: "ca9d3311-7446-4615-b071-5fe05f4e1be9", name: "Eating Out" },
    { id: "0bc45cf5-6dc7-4cfb-bae4-b740816862ac", name: "Equity investment", color: "958e80" },
    { id: "3e384a41-01f2-4978-b10e-73dba7cb6878", name: "Financing Expense", color: "958e80" },
    { id: "e9641f65-f89a-4278-b490-4f2c4fa6e2f7", name: "Financing Income", color: "958e80" },
    { id: "e4cb1102-1e2d-407e-b986-a7f14a7f0835", name: "General Payment", color: "ffbf84" },
    { id: "ed46831b-5c6d-49d5-a6a5-8e764ffa9b14", name: "Insurance", color: "ffbf84" },
    { id: "f0fd67cd-8c38-4883-8a39-529974b6f3a1", name: "Internal Transfers" },
    { id: "f805cb0b-af81-419b-a336-df6d79f16093", name: "Inventory", color: "ff6955" },
    { id: "f3e86a41-ccd8-4976-82c9-aed0a1ea2989", name: "Legal", color: "ffbf84" },
    { id: "536d9623-863d-4011-a04a-31e85066810d", name: "Operating Expenses", color: "ffbf84" },
    { id:  "42169f4c-9aea-47f1-ab98-f41d379b0bad", name: "Other Expenses", color: "ffbf84" },
    { id: "0ca2a326-085d-45c8-8a15-9e72f2598104", name: "Overdraft/NSF Fees", color: "ffbf84" },
    { id: "c134faed-5c47-4b76-8439-3f3c808ef782", name: "Payroll and Consultants", color: "f6f2ab" },
    { id: "c3189eec-282a-4680-abab-006611b02d76", name: "Personnel", color: "f6f2ab" },
    { id: "292c404b-a504-4bb7-abcd-a50816620330", name: "Postage", color: "ffbf84" },
    { id: "013d830c-ee70-4a0b-81dd-1edce790f435", name: "Reconciled Intra-Company Transfers" },
    { id: "c8b4af5c-916f-432f-b7b5-f4e9a351e688", name: "Refunds", color: "acdcff" },
    { id: "0afaa716-66a6-41ed-a577-1cc4bc6ed076", name: "Rent", color: "ffbf84" },
    { id: "80304577-2f6b-481f-bf46-28d243046b66", name: "Revenue", color: "75b970" },
    { id: "761ef302-53e5-4d3b-a076-7292ac19f0fd", name: "Salary Taxes", color: "f6f2ab" },
    { id: "8d92fd50-06b8-494c-b732-cd0b98f10b3c", name: "Sales and Marketing", color: "7048a3" },
    { id: "11bb7aa7-61e6-4753-b3f2-365f83694417", name: "Social Security Contributions", color: "f6f2ab" },
    { id: "d2f6df72-a6e6-4fc2-9221-9864406447f0", name: "Software", color: "ffbf84" },
    { id: "0ad649ed-175e-45c2-9194-2a78f3bf219c", name: "Special Inflows" },
    { id: "cb55a2e0-0299-449d-bd81-6d1a71e8b0da", name: "Special Outflows" },
    { id: "7e1a5138-67b3-4d57-8b1c-e69ffee2e126", name: "Tax Refund", color: "acdcff" },
    { id: "66faa5fe-b264-48b9-9a0d-e8ef27b10cf8", name: "Taxes", color: "f3e7cf" },
    { id: "e5e088d9-ab98-450c-be61-ef7b85940a0b", name: "Travel", color: "ffbf84" },
    { id: "2059e527-77e3-443e-80f7-d38dc9656b55", name: "Unreconciled Intra-Company Transfers" },
    { id: "a5f851cb-f3ff-41e3-8629-929395e65d50", name: "Utilities", color: "ffbf84" },
    { id: "2ecef92e-954e-4b42-9ce0-9e25194a53d5", name: "Vendors", color: "ff6955" }
  ];
  const bankList = [
    { id: "c5f3f93c-bf33-402e-a9f1-c15c148d451b", name: "Anonymous", bank: "iBank" },
    { id: "4d4e9f76-7d97-42a8-b1ec-360a127acb27", name: "Checking Account (EUR)", bank: "Airbank" },
    { id: "3f2e59a4-8620-4a8a-8ffc-c3092c907cb0", name: "Checking Account (GBP)", bank: "Airbank" },
    { id: "313ac86b-337c-484f-9a81-0e1c207699aa", name: "Mr. Kevin (Bills)", bank: "Business SuperBank" },
    { id: "a3bbb581-7e91-488e-a416-8ac8f5f6f5be", name: "Mr. Kevin (Household)", bank: "Business SuperBank" },
    { id: "c6c356b7-be49-4038-b19b-f29ff951122d", name: "Mr. Keysha Kayner", bank: "Business SuperBank" },
    { id: "ea78ff5d-7637-4f34-8ad9-a4ee767ef8d6", name: "Mr. Keysha Kayner", bank: "Business SuperBank" },
    { id: "387787f6-4ecd-4e36-b4ea-3a4ddee76465", name: "Mr. Keysha Kayner", bank: "Business SuperBank" },
    { id: "8ad7c58e-ce53-450b-90c8-6faf539c5f16", name: "Mr. Keysha Kayner", bank: "Business SuperBank" },
    { id: "03eedf59-b87d-40b0-abef-4ef265545808", name: "Mr. Navjot Higday", bank: "Business SuperBank" },
    { id: "63c66f69-97d0-40ca-862e-eba7b79d2d35", name: "Mr. Navjot Higday", bank: "Business SuperBank" },
    { id: "a45df2f6-4a32-42f2-946b-e6def523db41", name: "Mr. Navjot Higday", bank: "Business SuperBank" },
    { id: "0ebf6960-e67b-4a7e-b384-c64f62a17c55", name: "Mr. Navjot Higday", bank: "Business SuperBank" },
    { id: "9ec5832f-9f3e-4c65-b8f8-53fda6bdfc17", name: "Mr. Tahir Hagee", bank: "Business SuperBank" },
    { id: "9dd54f3f-a96f-4ab2-8fda-84e6ac83f38e", name: "Mr. Tahir Hagee", bank: "Business SuperBank" },
    { id: "343db37d-804e-4892-bf7d-bc36f601706a", name: "Nombre Apellido", bank: "iBank" },
    { id: "a3ebeb0e-d191-41d1-a271-ab147ff32e7c", name: "Sydney Beard", bank: "The Sandbox" },
    { id: "1bb1aa71-bd04-42a1-a0b6-d9b875d67694", name: "Sydney Beard", bank: "The Sandbox" },
    { id: "02b2a5a5-bfd3-40a3-bb20-30c497af1a5d", name: "Sydney Beard", bank: "The Sandbox" },
    { id: "bb3e0261-bdde-49c1-9752-5ea55a61ca33", name: "Sydney Beard", bank: "The Sandbox" },
  ];
  const columns = [
    {
      title: 'Reference',
      key: 'reference',
      dataIndex: 'reference',
      scopedSlots: { customRender: 'reference' }
    },
    {
      title: 'Category',
      key: 'categoryId',
      dataIndex: 'categoryId',
      scopedSlots: { customRender: 'category' }
    },
    {
      title: 'Date',
      key: 'date',
      dataIndex: 'date',
    },
    {
      title: 'Amount',
      key: 'amount',
      dataIndex: 'amount',
      align: 'right',
      scopedSlots: { customRender: 'amount' }
    },
  ];
  const data = [
    { id: '4f87e7c0-2831-4a38-9b94-dc94f2e8554d', accountId: 'a3bbb581-7e91-488e-a416-8ac8f5f6f5be', reference: '', categoryId: '0ca2a326-085d-45c8-8a15-9e72f2598104', amount: -4.62, currency: "GBP", date: '2017-09-01 23:00:00' },
    { id: "4f87e7c0-2831-4a38-9b94-dc94f2e8554d", accountId: "a3bbb581-7e91-488e-a416-8ac8f5f6f5be", categoryId: "0ca2a326-085d-45c8-8a15-9e72f2598104", amount: -4.69, currency: "GBP", date: "2017-09-01 23:00:00.000" },
    { id: "56b80608-ed28-4182-a509-09ff9deb3ec9", accountId: "a3bbb581-7e91-488e-a416-8ac8f5f6f5be", categoryId: "11bb7aa7-61e6-4753-b3f2-365f83694417", amount: -3.99, currency: "GBP", date: "2017-09-01 23:00:00.000" },
    { id: "1a74ff6b-2c56-4c88-9f4e-7e1399092d9b", accountId: "313ac86b-337c-484f-9a81-0e1c207699aa", categoryId: "e5e088d9-ab98-450c-be61-ef7b85940a0b", amount: -3.99, currency: "EUR", date: "2017-09-01 23:00:00.000" },
    { id: "39ef604d-4e9b-4caa-bdee-7423871601df", accountId: "313ac86b-337c-484f-9a81-0e1c207699aa", categoryId: "b61354fc-c493-46fe-8edd-87da4f503965", amount: -4.69, currency: "EUR", date: "2017-09-01 23:00:00.000" },
    { id: "0ede387c-003a-4c9b-a16c-69aec80db524", accountId: "a3bbb581-7e91-488e-a416-8ac8f5f6f5be", categoryId: "292c404b-a504-4bb7-abcd-a50816620330", amount: -39.05, currency: "GBP", date: "2017-09-02 23:00:00.000" },
    { id: "a3a3dbb3-c46a-4e0e-9208-e25b35c4b985", accountId: "313ac86b-337c-484f-9a81-0e1c207699aa", categoryId: "b61354fc-c493-46fe-8edd-87da4f503965", amount: -39.05, currency: "EUR", date: "2017-09-02 23:00:00.000" },
    { id: "848d8ef6-83fd-4662-92b7-075081f2a025", accountId: "a3bbb581-7e91-488e-a416-8ac8f5f6f5be", categoryId: "f805cb0b-af81-419b-a336-df6d79f16093", amount: -63.73, currency: "GBP", date: "2017-09-06 23:00:00.000" },
    { id: "97cd6fbf-9146-4853-8d65-18a546f1f762", accountId: "313ac86b-337c-484f-9a81-0e1c207699aa", categoryId: "0ca2a326-085d-45c8-8a15-9e72f2598104", amount: -63.73, currency: "EUR", date: "2017-09-06 23:00:00.000" },
    { id: "3ec95623-9267-4f34-9ffb-4f1c1eec2afe", accountId: "a3bbb581-7e91-488e-a416-8ac8f5f6f5be", categoryId: "cb55a2e0-0299-449d-bd81-6d1a71e8b0da", amount: -0.12, currency: "GBP", date: "2017-09-06 23:00:00.000" },
    { id: "b579f728-e854-4069-b62f-a8775aa78e78", accountId: "313ac86b-337c-484f-9a81-0e1c207699aa", categoryId: "a5f851cb-f3ff-41e3-8629-929395e65d50", amount: -0.12, currency: "EUR", date: "2017-09-06 23:00:00.000" },
    { id: "0488f712-9935-4973-8d69-f11c2edf2aed", accountId: "313ac86b-337c-484f-9a81-0e1c207699aa", categoryId: "0ca2a326-085d-45c8-8a15-9e72f2598104", amount: -42.71, currency: "EUR", date: "2017-09-08 23:00:00.000" },
    { id: "d83555a5-9078-41d8-951d-d165bbb1c5a5", accountId: "a3bbb581-7e91-488e-a416-8ac8f5f6f5be", categoryId: "292c404b-a504-4bb7-abcd-a50816620330", amount: -0.12, currency: "GBP", date: "2017-09-08 23:00:00.000" },
    { id: "bc226d20-9276-413d-889c-db41086effa3", accountId: "313ac86b-337c-484f-9a81-0e1c207699aa", categoryId: "e4cb1102-1e2d-407e-b986-a7f14a7f0835", amount: -0.12, currency: "EUR", date: "2017-09-08 23:00:00.000" },
    { id: "8944125f-06ba-4438-a8ef-fd3955862777", accountId: "a3bbb581-7e91-488e-a416-8ac8f5f6f5be", categoryId: "350a5a32-beee-47f3-bb71-3814d4a7082a", reference: "Electer Bander Hostelry",amount: -42.71, currency: "GBP", date: "2017-09-08 23:00:00.000" },
    { id: "72518d6f-28d5-44f3-9a23-55ad4c0064f1", accountId: "313ac86b-337c-484f-9a81-0e1c207699aa", categoryId: "80304577-2f6b-481f-bf46-28d243046b66", amount: 3835.25, currency: "EUR", date: "2017-09-11 23:00:00.000" },
    { id: "6c6f1e93-ec49-4a64-a19b-37abe849da16", accountId: "a3bbb581-7e91-488e-a416-8ac8f5f6f5be", categoryId: "2059e527-77e3-443e-80f7-d38dc9656b55", amount: 3835.25, currency: "GBP", date: "2017-09-11 23:00:00.000" },
    { id: "6a0b25c1-2a53-4acc-88a0-3c17af3e58e3", accountId: "a3bbb581-7e91-488e-a416-8ac8f5f6f5be", categoryId: "0c600155-27a0-40ce-9df5-523f2831ff56", reference: "Pantries Uvulatomy Catamenial", amount: -27.56, currency: "GBP", date: "2017-09-13 23:00:00.000" },
    { id: "90d1901b-2fc5-4b08-8937-1d31ad08235c", accountId: "313ac86b-337c-484f-9a81-0e1c207699aa", categoryId: "536d9623-863d-4011-a04a-31e85066810d", amount: -0.57, currency: "EUR", date: "2017-09-13 23:00:00.000" },
    { id: "d90766a5-69dc-4594-8c8c-6c561913c287", accountId: "313ac86b-337c-484f-9a81-0e1c207699aa", categoryId: "0bc45cf5-6dc7-4cfb-bae4-b740816862ac", amount: -27.56, currency: "EUR", date: "2017-09-13 23:00:00.000" },
    { id: "cdd4e6c6-b290-4113-973c-59f62ad0c98e", accountId: "a3bbb581-7e91-488e-a416-8ac8f5f6f5be", categoryId: "Irreconcilable Millrind Statutably", amount: -0.57, currency: "GBP", date: "2017-09-13 23:00:00.000" },
    { id: "3a429194-3554-46dc-90c1-f45f2929436d", accountId: "a3bbb581-7e91-488e-a416-8ac8f5f6f5be", categoryId: "6ad0e563-7f94-417d-8370-7eca2e52b2cc", amount: -17.45, currency: "GBP", date: "2017-09-14 23:00:00.000" },
  ];

  export default {
    name: 'NuxtTutorial',
    components: {
      Button,
      Input,
      Select,
      Option,
      DatePicker,
      Table,
    },

    data() {
      return {
        loading: false,
        searchValue: '',
        currentBank: '',
        currentCategory: '',
        categories: categoryList,
        banks: bankList,
        startMonth: Number(moment().format('YYYYMM01')),
        endMonth: Number(moment().format('YYYYMM01')),
        tableColumns: columns,
        currentTransactions: [],
        transactions: data.map((d) => ({ ...d, date: moment(d.date, 'YYYY-MM-DD hh:mm:ss').format('DD/MM/YYYY') })),
      }
    },
    mounted() {
      // this.getTransactions();
      // this.getCategories();
      // this.getBanks();
      this.currentTransactions = this.transactions;
    },
    methods: {
      // getCategories () {
      //   if (this.loading) {
      //     return
      //   }
      //
      //   this.loading = true;
      //   axios('localhost:3100/api/categories').then(
      //     (response) => {
      //       this.loading = false;
      //       this.categories = response.data;
      //       console.log('otohfi:::::::', response);
      //     },
      //     (error) => {
      //       this.loading = false;
      //       console.error(error.response.data.message)
      //     }
      //   )
      // },
      // getBanks () {
      //   if (this.loading) {
      //     return
      //   }
      //
      //   this.loading = true;
      //   axios.get('/api/banks').then(
      //     (response) => {
      //       this.loading = false;
      //       this.banks = response.data;
      //     },
      //     (error) => {
      //       this.loading = false;
      //       console.error(error.response.data.message)
      //     }
      //   )
      // },
      // getTransactions () {
      //   if (this.loading) {
      //     return
      //   }
      //
      //   this.loading = true;
      //   axios.get('/api/transactions').then(
      //     (response) => {
      //       this.loading = false;
      //       this.transactions = response.data;
      //     },
      //     (error) => {
      //       this.loading = false;
      //       console.error(error.response.data.message)
      //     }
      //   )
      // },
      handleSearch(e) {
        const text = e.target.value;
        this.searchValue = text;
        this.currentTransactions = this.transactions.filter((t) => t.reference?.toUpperCase().includes(text.toUpperCase()));
      },
      handleBank(e) {
        this.currentBank = e;
        this.currentTransactions = this.transactions.filter((t) => t.accountId === e);
      },
      handleCategory(e) {
        this.currentCategory = e;
        this.currentTransactions = this.transactions.filter((t) => t.accountId === e);
      },
      handleStartDate(date, dateString) {
        const year = moment(date).year();
        const month = moment(date).month() + 1;
        this.startMonth = Number(`${year}${month > 9 ? month : `0${month}`}01`);
        this.filterByDate();
      },
      handleEndDate(date, dateString) {
        const year = moment(date).year();
        const month = moment(date).month() + 1;
        this.endMonth = Number(`${year}${month > 9 ? month : `0${month}`}01`);
        this.filterByDate();
      },
      filterByDate() {
        this.currentTransactions = this.transactions.filter((t) => Number(moment(t.date, 'DD/MM/YYYY').format('YYYYMMDD')) >= this.startMonth);
        this.currentTransactions = this.currentTransactions.filter((t) => Number(moment(t.date, 'DD/MM/YYYY').format('YYYYMMDD')) <= this.endMonth);
      },
      goDetail() {
        console.log('otohfi::::::::::');
        this.$router.push('/detail');
      }
    }
  }
</script>

<style>
  .page-wrapper {
    width: 100%;
    height: 100%;
    padding: 20px;
  }
  .title {
    margin-bottom: 10px;
    font-size: 25px;
    font-weight: 700;
    color: black;
  }
  .filter-layer {
    display: flex;
    align-items: flex-start;
    justify-content: space-between;
  }
  .filter-item {
    display: flex;
    flex-direction: column;
    align-items: flex-start;
  }
  .filter-item.max {
    width: 40%;
  }
  .filter-item.middle {
    width: 15%;
  }
  .filter-item.min {
    width: 10%;
  }
  .select-wrapper {
    width: 100%;
  }
  .transaction-list {
    margin-top: 10px;
    max-height: 80vh;
    overflow-y: auto;
  }

  .ant-table {
    color: black;
  }
  .ant-table-thead > tr > th {
    color: #aeaeae;
    border-top: 1px solid #e8e8e8;
    background: white;
  }
  .category-chip {
    border-radius: 5px;
    padding: 8px 10px;
    color: #3d3d3d;
  }
  .td-amount {
    font-weight: 600;
  }
  .td-currency {
    margin-left: 5px;
    color: #aeaeae;
  }
</style>
