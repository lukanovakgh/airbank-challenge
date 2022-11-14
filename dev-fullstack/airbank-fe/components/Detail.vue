<template>
  <div class="page-wrapper">
    <div class="back-button" @click="goBack"><</div>
    <div class="title">Transaction - {{ data.id }}</div>
    <div class="property-list">
      <div class="item">
        <span class="label">ID:</span>
        <div class="property">{{ data.id }}</div>
      </div>
      <div class="item">
        <span class="label">Account:</span>
        <div class="property">{{ data.accountId }}</div>
      </div>
      <div class="item">
        <span class="label">Category:</span>
        <div class="property">{{ data.categoryId }}</div>
      </div>
      <div class="item">
        <span class="label">Reference:</span>
        <div class="property">{{ data.reference ? data.reference : 'No reference provided' }}</div>
      </div>
      <div class="item">
        <span class="label">Amount:</span>
        <div class="property">{{ data.amount }}</div>
      </div>
      <div class="item">
        <span class="label">Currency:</span>
        <div class="property">{{ data.currency }}</div>
      </div>
      <div class="item">
        <span class="label">Date:</span>
        <div class="property">{{ data.date }}</div>
      </div>
    </div>
  </div>
</template>

<script>
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

    data() {
      return {
        id: '',
        data: data[0],
      }
    },
    mounted() {
    },
    methods: {
      goBack() {
        this.$router.push('/');
      },
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
  .back-button {
    display: flex;
    align-items: center;
    justify-content: center;
    margin-bottom: 20px;
    border-radius: 50%;
    width: 30px;
    height: 30px;
    font-size: 20px;
    font-weight: 700;
    color: white;
    background: gray;
    cursor: pointer;
  }
  .back-button:hover {
    opacity: 0.8;
  }
  .property-list {
    display: flex;
    flex-direction: column;
    margin-top: 20px;
  }
  .item {
    display: flex;
    align-items: center;
    margin: 5px 0;
  }
  .label {
    margin-right: 10px;
    font-size: 20px;
    color: gray;
  }
  .property {
    font-size: 18px;
    color: black;
  }
</style>
