<style>
.input-box {
  width: 100%;
  height: 38px;
  font-size: 1rem;
  padding: 0.5rem;
  box-sizing: border-box;
  border: 1px solid #ccc;
  border-radius: 4px;
}



@media print {
  .no-print {
    display: none;
  }

  @page {
    size: auto;
    margin: 0;
  }
}
</style>


<template>
  <div>
    <div class="py-4">
      <div class="px-14 py-6 flex justify-between items-start">
        <div>
          <img src="/src/assets/brand-sample.png" class="h-12" />
        </div>
        <div class="text-sm flex items-center space-x-6">
          <div class="border-r pr-4 text-right">
            <p class="whitespace-nowrap text-slate-400">Date</p>
            <p class="whitespace-nowrap font-bold text-main">{{ currentDate }}
            </p>
          </div>
          <div class="pl-4 text-right">
            <p class="whitespace-nowrap text-slate-400">Invoice #</p>
            <p class="whitespace-nowrap font-bold text-main" @dblclick="enableSingleEdit($event)">
              BRA-00335
            </p>
          </div>
        </div>
      </div>

      <div class="bg-slate-100 px-14 py-6 text-sm flex justify-between">
        <div class="text-neutral-600">
          <p class="font-bold editable" @dblclick="enableSingleEdit($event)">Supplier Company INC</p>
          <p class="editable" @dblclick="enableSingleEdit($event)">+880123456789</p>
          <p class="editable" @dblclick="enableSingleEdit($event)">john@email.com</p>
          <p class="editable" @dblclick="enableSingleEdit($event)">VAT: 23456789</p>
          <p class="editable" @dblclick="enableSingleEdit($event)">Vandervort Spurs, United States</p>
        </div>
        <div class="text-right text-neutral-600">
          <p class="font-bold editable" @dblclick="enableSingleEdit($event)">Mr. John Smith</p>
          <p class="editable" @dblclick="enableSingleEdit($event)">+880123456789</p>
          <p class="editable" @dblclick="enableSingleEdit($event)">john@email.com</p>
          <p class="editable" @dblclick="enableSingleEdit($event)">Vandervort Spurs, United States</p>
        </div>
      </div>

      <div class="px-14 py-10 text-sm text-neutral-700">
        <table class="w-full border-collapse border-spacing-0">
          <thead class="border border-t-2">
            <tr>
              <td class="border-b-2 border-main py-3 pl-3 font-bold text-main">#</td>
              <td class="border-b-2 border-main py-3 pl-2 font-bold text-main">Product details</td>
              <td class="border-b-2 border-main py-3 pl-2 text-right font-bold text-main">Price</td>
              <td class="border-b-2 border-main py-3 pl-2 text-center font-bold text-main">Qty.</td>
              <td class="border-b-2 border-main py-3 pr-3 text-right font-bold text-main">Subtotal</td>
            </tr>
          </thead>
          <tbody>
            <tr class="border-b-1" v-for="(item, index) in products" :key="index">
              <td class="border-b py-3 pl-3">{{ index + 1 }}.</td>
              <td class="border-b py-3 pl-2 editable" @dblclick="enableEdit($event, index, 'productName')">{{
                item.productName }}</td>
              <td class="border-b py-3 pl-2 text-right editable" @dblclick="enableEdit($event, index, 'price')">{{
                item.price }}</td>
              <td class="border-b py-3 pl-2 text-center editable" @dblclick="enableEdit($event, index, 'quantity')">{{
                item.quantity }}</td>
              <td class="border-b py-3 pr-3 text-right">{{ item.price * item.quantity }}</td>
            </tr>

            <tr>
              <td>
                <div class="text-neutral-600 ">
                  <button class=" bg-blue-500 text-white text-md mt-2 px-10 py-1 rounded-md no-print"
                    @click="addProduct()">Add</button>

                </div>
              </td>
              <td class="border-white"></td>
              <td class="border-white"></td>
              <td class="border-white"></td>
              <td class="border-white"></td>
            </tr>

            <tr>
              <td class="border-white"></td>
              <td class="border-white"></td>
              <td class="border-white"></td>
              <td class="border-white"></td>
              <td>
                <div class="text-neutral-600 flex justify-between">
                  <p class="font-bold">Total:</p>
                  <p class="font-bold">${{ total }}</p>
                </div>
              </td>
            </tr>

            <tr>
              <td class="border-white"></td>
              <td class="border-white"></td>
              <td class="border-white"></td>
              <td class="border-white"></td>
              <td>
                <div class="text-neutral-600 flex justify-between" @click="vatPercentage++">
                  <p class="font-bold">Total VAT:</p>
                  <p class="font-bold">{{ vatPercentage }}%</p>
                </div>
              </td>
            </tr>

            <tr>
              <td class="border-white"></td>
              <td class="border-white"></td>
              <td class="border-white"></td>
              <td class="border-white"></td>
              <td>
                <div class="text-neutral-600 flex justify-between">
                  <p class="font-bold">Total Due:</p>
                  <p class="font-bold">${{ totalDue }}</p>
                </div>
              </td>
            </tr>
          </tbody>
        </table>
        <div class="mt-5 text-neutral-600 flex space-x-2 items-center ">
          <p class="font-bold inline-block text-center">Payment Method:</p>
          <select class="bg-white text-md border-none w-52 appearance-none">
            <option value="Bank Transfer">Bank Transfer</option>
            <option value="Cash">Cash</option>
          </select>
        </div>
        <button class="bg-blue-500 text-white text-md mt-2 px-10 py-1 rounded-md align-self-center no-print"
          @click="invoicePrint">Print
          Invoice</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      products: [
        { productName: '', price: 0.00, quantity: 1 },
      ],
      vatPercentage: 0,

      currentDate: new Date().toLocaleDateString('en-US', {
        year: 'numeric',
        month: 'long',
        day: 'numeric'
      })
    };
  },
  computed: {
    total() {
      let total = 0;
      for (const item of this.products) {
        total += item.price * item.quantity;
      }
      return total.toFixed(2);
    },
    totalDue() {
      const vatAmount = (this.total * this.vatPercentage) / 100;
      return (parseFloat(this.total) + vatAmount).toFixed(2);
    }
  },
  methods: {

    addProduct() {
      // Add a new product to the products array
      this.products.push({ productName: '', price: 0.00, quantity: 1 });
    },
    enableEdit(event, index, field) {
      const element = event.target;
      const currentValue = element.textContent.trim();

      const input = document.createElement('input');
      input.type = 'text';
      input.value = currentValue;
      input.className = 'input-box';
      input.addEventListener('blur', () => this.saveEdit(input, element, index, field));
      input.addEventListener('keypress', (e) => {
        if (e.key === 'Enter') this.saveEdit(input, element, index, field);
      });

      element.replaceWith(input);
      input.focus();
    },

    saveEdit(input, element, index, field) {
      const newValue = input.value.trim();
      if (field === 'price' || field === 'quantity') {
        this.products[index][field] = parseFloat(newValue) || 0; // Ensure it's a number
      } else {
        this.products[index][field] = newValue;
      }
      element.textContent = this.products[index][field];
      input.replaceWith(element);
    },

    enableSingleEdit(event) {
      const element = event.target;
      const currentValue = element.textContent.trim();

      const input = document.createElement('input');
      input.type = 'text';
      input.value = currentValue;
      input.className = 'input-box';
      input.addEventListener('blur', () => this.saveSingleEdit(input, element));
      input.addEventListener('keypress', (e) => {
        if (e.key === 'Enter') this.saveSingleEdit(input, element);
      });

      element.replaceWith(input);
      input.focus();
    },

    saveSingleEdit(input, element) {
      const newValue = input.value.trim();
      element.textContent = newValue;
      input.replaceWith(element);
    },
    invoicePrint() {
      window.print();

    }

  }
}
</script>
