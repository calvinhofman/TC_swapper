<template>
  <div class="sm:px-0 mx-auto mt-12">
    <div class="successAlert" id="alertBox">
      {{ alertMessage }}
    </div>
    <div class="flex flex-col md:flex-row justify-center text-black items-center w-11/12 mx-auto">
      <div>
        <!-- <ConnectWallet theme="white" /> -->
        <w3m-core-button></w3m-core-button>
      </div>
    </div>

    <div class="flex flex-col md:flex-row justify-between space-y-8 md:space-y-0 md:space-x-8 mt-12 mx-auto">
      <div id="card" class="border-4 rounded-xl border-blue w-full md:w-12/12 bg-gray-100 mx-auto pb-6">
        <div class="px-6 mt-6">
          <div class="text-black  w-full border-2 rounded-xl border-gray-400 bg-white p-4">
            <p class="text-center text-2xl font-bold">Presale</p>
            <div class="mt-2">
              <div class="flex">
                <input
                  class="px-4 py-2 border w-full border-gray-300 rounded-l-md focus:outline-none focus:ring focus:border-blue-300"
                  type="number" placeholder="amount" :value="amountUSD" @input="setAmountUSD" />
                <button class="mx-2" @click="setMaximumAmount">Max</button>

                <select class="w-20" v-model="selectedOption">
                  <option default disabled value="">Please select an option</option>
                  <option v-for="option in options" :value="option" :key="option.label">
                    {{ option.coin }}
                  </option>
                </select>
              </div>
              <div>
                <span> {{ balanceOfToken }} {{ selectedOption.label }}</span>
              </div>
              <div
                class="flex mt-4 text-white font-bold text-2xl flex-col sm:flex-row items-center justify-between space-x-0 space-y-4 sm:space-y-0 sm:space-x-4">
                <button v-if="canClaim" @click="withdrawTokens" class="bg-[#48CF3C] w-full p-2 px-4 rounded-xl">
                  Claim
                </button>
                <button v-else @click="depositTokens" class="bg-[#04121d] w-full p-2 px-4 rounded-xl">
                  Contribute
                </button>
              </div>
              <div class="mt-8">
                <p class="font-semibold">Presale Info:</p>
                <div class="flex flex-col w-full justify-between">
                  <div class="flex flex-row justify-between">
                    <p>Round: </p>
                    <p>{{ rounds }}</p>
                  </div>
                  <div class="flex flex-row justify-between">
                    <p>Tokens sold: </p>
                    <p>{{ totalSold }} $TCK</p>
                  </div>
                  <div class="flex flex-row justify-between">
                    <p>Total $ in presale: </p>
                    <p>{{ totalUSDInvested }}</p>
                  </div>
                  <div class="flex flex-row justify-between">
                    <p>Sale live: </p>
                    <p>live</p>
                  </div>
                </div>
                <div class="flex flex-col sm:flex-row sm:justify-center items-center">
                  <div>
                    Powered by <a href="https://metalabz.gg">MetaLabz</a>
                  </div>
                  <div>
                    <img class="w-8 h-8" src="https://metalabz.gg/favicon.ico" alt="" />
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
/* eslint-disable */

/* wallet connect config */
import { EthereumClient, w3mConnectors, w3mProvider } from '@web3modal/ethereum'
import { Web3Modal } from '@web3modal/html'
import { configureChains, createConfig, prepareWriteContract, writeContract, watchAccount, readContract, getAccount, fetchBalance } from '@wagmi/core'


import { bsc } from '@wagmi/core/chains'
import { tokenABI, stableABI } from './abis.js'
const chains = [bsc]
const projectId = 'bf13f5490d12eaaa6a13a1b1038e8155'

const { publicClient } = configureChains(chains, [w3mProvider({ projectId })])
const wagmiConfig = createConfig({
  autoConnect: true,
  connectors: w3mConnectors({ projectId, version: 1, chains }),
  publicClient
})
const ethereumClient = new EthereumClient(wagmiConfig, chains)
const web3modal = new Web3Modal({ projectId }, ethereumClient)

import Web3 from "web3";
const w3Provider = new Web3("https://bsc-dataseed.binance.org/")

const contractAddress = "0x1fe32255588792b9a6ca73c36e97fe9deefa283b";
const contractAddressBUSD = "0xe9e7CEA3DedcA5984780Bafc599bD69ADd087D56";
const contractAddressUSDC = "0x8AC76a51cc950d9822D68b83fE1Ad97B32Cd580d";
const contractAddressBSC = "0x55d398326f99059fF775485246999027B3197955";
// const stakingContract = new w3Provider.eth.Contract(abis, stakingAddr)
// const nftContract = new w3Provider.eth.Contract(nftABI, nftAddr)
// const tokenContract = new w3Provider.eth.Contract(tokenABI, tokenAddr)
export default {
  name: "TC-swapper",


  data() {
    return {
      userWallet: "",
      web3: null,
      provider: null,
      stakingActive: "",
      viewUnpaidDividends: 0,
      viewTotalDividends: 0,
      lockAPRs: "7",
      lockAPRsFront: 0,
      balanceOf: "",
      balanceOfToken: "",
      selectedValue: "6",
      userLock: "0",
      amount: 0,
      selectedLockPeriod: '',
      contract: null,
      contractNFT: null,
      contractToken: null,
      bonusThreshold: 0,
      nftCount: 0,
      nftBonusAmount: 0,
      endDate: null,
      stakedAmount: 0,
      sliderValue: 0,
      contractAddress: null,
      contractAddressBUSD: null,
      contractAddressUSDC: null,
      contractAddressBSC: null,
      web3: null,
      contract: null,
      totalUSDInvested: 0.,
      contractTransfer: null,
      contractBUSD: null,
      contractUSDC: null,
      contractBSC: null,
      selectedOption: null,
      amountUSD: null,
      rounds: null,
      totalSold: null,
      canClaim: null,
      availableAmount: null,
      address: null,
      data: null,
      fetchInfo: null,
      rounds: null,
      handleSelectChange: null,
      options: null,
      userWallet: undefined,
      alertMessage: "Alert Box",
      selectedOption: '',
      isapprovedBUSD: null,
      isapprovedUSDC: null,
      isapprovedUSDT: null,
      bnbAmount: null,
      options: [
        {
          value: "0xe9e7CEA3DedcA5984780Bafc599bD69ADd087D56",
          coin: "BUSD",
          label: "BUSD"
        },
        {
          value: "0x8AC76a51cc950d9822D68b83fE1Ad97B32Cd580d",
          coin: "USDC",
          label: "USDC"

        },
        {
          value: "bnb",
          coin: "BNB",
          label: "BNB"

        },
        {
          value: "0x55d398326f99059fF775485246999027B3197955",
          coin: "BSC-USD",
          label: "BSC-USD"

        },
      ],
    };
  },
  created() {
  },
  watch: {
    selectedOption(newOption) {
      this.checkSelectedOption(newOption);
    },
  },
  mounted() {
    const alertBox = document.getElementById('alertBox');

    watchAccount((curAccount) => {
      if (curAccount.address) {
        this.userWallet = curAccount.address;
        this.getuserInfo();
      } else {
        this.userWallet = null;
      }
    });
  },

  methods: {

    showAlertBox(alertType, setMessage) {
      this.alertMessage = setMessage;
      console.log('dasdfasklfgasd')
      if (alertType == 'error') {
        alertBox.className = "errorAlert";
        setTimeout(() => { alertBox.style.display = 'none'; }, 4000);
      } else if (alertType == 'process') {
        this.alertMessage = "Transaction Processing"
        alertBox.className = "processAlert";
      } else {
        alertBox.className = "successAlert";
        setTimeout(() => { alertBox.style.display = 'none'; }, 4000);
      }
      alertBox.style.display = 'block';
    },

    async getuserInfo() {
      let curAccount = getAccount();
      this.userWallet = curAccount.address;
      let bnbAmount = await fetchBalance({
        address: this.userWallet
      })
      this.bnbAmount = bnbAmount
      console.log(this.bnbAmount)

      console.log(this.userWallet)
      this.rounds = await readContract({
        address: contractAddress,
        abi: tokenABI,
        functionName: 'curRound',
      });

      this.totalUSDInvested = await readContract({
        address: contractAddress,
        abi: tokenABI,
        functionName: 'totalUSDCollected',
      });

      this.totalSold = await readContract({
        address: contractAddress,
        abi: tokenABI,
        functionName: 'totalTokensSold'
      })

      this.canClaim = await readContract({
        address: contractAddress,
        abi: tokenABI,
        functionName: 'canClaim',
      })


      this.isapprovedBUSD = await readContract({
        address: contractAddressBUSD,
        abi: stableABI,
        functionName: 'allowance',
        args: [this.userWallet, contractAddress]
      })

      this.isapprovedBSC = await readContract({
        address: contractAddressBSC,
        abi: stableABI,
        functionName: 'allowance',
        args: [this.userWallet, contractAddress]
      })

      this.isapprovedUSDC = await readContract({
        address: contractAddressUSDC,
        abi: stableABI,
        functionName: 'allowance',
        args: [this.userWallet, contractAddress]
      })

      console.log(this.isapprovedBUSD.toString())
      console.log(this.isapprovedBSC.toString())
      console.log(this.isapprovedUSDC.toString())


      this.totalSold = this.formatNumber(Web3.utils.fromWei(this.totalSold, 'ether'))
      let formatted = Web3.utils.fromWei(this.totalUSDInvested, 'ether')
      this.totalUSDInvested = this.formatNumber(formatted)
    },

    async checkSelectedOption(option) {
      if (option.value == '0xe9e7CEA3DedcA5984780Bafc599bD69ADd087D56') {
        this.balanceOfToken = await readContract({
          address: contractAddressBUSD,
          abi: stableABI,
          functionName: 'balanceOf',
          args: [this.userWallet]

        })
        this.balanceOfToken = Web3.utils.fromWei(this.balanceOfToken, 'ether')
      }
      if (option.value == '0x55d398326f99059fF775485246999027B3197955') {
        this.balanceOfToken = await readContract({
          address: contractAddressBSC,
          abi: stableABI,
          functionName: 'balanceOf',
          args: [this.userWallet]

        })
        this.balanceOfToken = Web3.utils.fromWei(this.balanceOfToken, 'ether')
      }
      if (option.value == '0x8AC76a51cc950d9822D68b83fE1Ad97B32Cd580d') {
        this.balanceOfToken = await readContract({
          address: contractAddressUSDC,
          abi: stableABI,
          functionName: 'balanceOf',
          args: [this.userWallet]

        })
        this.balanceOfToken = Web3.utils.fromWei(this.balanceOfToken, 'ether')
      }
      if (option.value == 'bnb') {
        this.balanceOfToken = this.bnbAmount.formatted;
        console.log(this.balanceOfToken)

      }
    },

    setMaximumAmount() {
      this.amountUSD = parseFloat(this.balanceOfToken - 0.005); // Update amountUSD with the input value

    },

    setAmountUSD(event) {
      this.amountUSD = parseFloat(event.target.value); // Update amountUSD with the input value
    },

    formatNumber(num) {
      return Number(num).toLocaleString("en-US", { maximumFractionDigits: 0 });
    },

    async depositTokens() {

      if (this.isapprovedBUSD > 0 && this.selectedOption.value === '0xe9e7CEA3DedcA5984780Bafc599bD69ADd087D56') {
        console.log(this.amountUSD)
        try {
          const { hash } = await writeContract({
            address: contractAddress,
            abi: tokenABI,
            functionName: 'buyTokens',
            args: [Web3.utils.toWei(this.amountUSD, 'ether'), this.selectedOption.value],
          })
          this.showAlertBox('success', 'tokens bought was successful');

          console.log(hash)

        } catch (error) {
          console.log(error)
          this.showAlertBox('error', 'Something went wrong');

        }
      } else if (this.isapprovedBUSD == 0 && this.selectedOption.value === '0xe9e7CEA3DedcA5984780Bafc599bD69ADd087D56') {
        try {
          const { hash } = await writeContract({
            address: contractAddressBUSD,
            abi: stableABI,
            functionName: 'approve',
            args: [contractAddress, Web3.utils.toWei(this.amountUSD, 'ether')],
          })
          console.log(hash)
          this.showAlertBox('success', 'approving of max amount successfull');

        } catch (error) {
          this.showAlertBox('error', 'approving of max amount failed');

          console.log(error)
        }
      }
      if (this.isapprovedBSC > 0 && this.selectedOption.value === '0x55d398326f99059fF775485246999027B3197955') {
        console.log(this.amountUSD)
        try {
          const { hash } = await writeContract({
            address: contractAddress,
            abi: tokenABI,
            functionName: 'buyTokens',
            args: [Web3.utils.toWei(this.amountUSD, 'ether'), this.selectedOption.value],
          })
          console.log(hash)
          this.showAlertBox('success', 'tokens bought was successful');

        } catch (error) {
          console.log(error)
          this.showAlertBox('error', 'something went wrong');

        }
      } else if (this.isapprovedBSC == 0 && this.selectedOption.value === '0x55d398326f99059fF775485246999027B3197955') {
        try {
          const { hash } = await writeContract({
            address: contractAddressBSC,
            abi: stableABI,
            functionName: 'approve',
            args: [contractAddress, Web3.utils.toWei(this.amountUSD, 'ether')],
          })
          console.log(hash)
          this.showAlertBox('success', 'approving of max amount successfull');

        } catch (error) {
          console.log(error)
          this.showAlertBox('error', 'approving of max amount failed');


        }
      }

      if (this.isapprovedBSC > 0 && this.selectedOption.value === '0x8AC76a51cc950d9822D68b83fE1Ad97B32Cd580d') {
        console.log(this.amountUSD)
        try {
          const { hash } = await writeContract({
            address: contractAddress,
            abi: tokenABI,
            functionName: 'buyTokens',
            args: [Web3.utils.toWei(this.amountUSD, 'ether'), this.selectedOption.value],
          })
          console.log(hash)
          this.showAlertBox('success', 'tokens bought was successful');

        } catch (error) {
          console.log(error)
          this.showAlertBox('error', 'something went wrong');

        }
      } else if (this.isapprovedBSC == 0 && this.selectedOption.value === '0x8AC76a51cc950d9822D68b83fE1Ad97B32Cd580d') {
        try {
          const { hash } = await writeContract({
            address: contractAddressUSDC,
            abi: stableABI,
            functionName: 'approve',
            args: [contractAddress, Web3.utils.toWei(this.amountUSD, 'ether')],
          })
          this.showAlertBox('success', 'approving of max amount successfull');
          console.log(hash)
        } catch (error) {
          console.log(error)
          this.showAlertBox('error', 'approving of max amount failed');

        }
      }
      if (this.selectedOption.value === 'bnb') {
        console.log(this.amountUSD)
        console.log('dfghjkahfljkadf')
        try {
          const { hash } = await writeContract({
            address: contractAddress,
            abi: tokenABI,
            functionName: 'buyTokens',
            args: [this.amountUSD, '0x0000000000000000000000000000000000000000'],
          })
          this.showAlertBox('success', 'tokens bought was successful');

          console.log(hash)

        } catch (error) {
          this.showAlertBox('error', 'something went wrong');

          console.log(error)

        }
      }
      if (!this.userWallet) {
        console.log('dasd')
        this.showAlertBox('error', 'connect wallet');
      }

      if (!this.userWallet || this.amountUSD === 0) {
        console.log('dasd')
        this.showAlertBox('error', 'fill amount');
      }




    },

    async withdrawTokens() {
      try {
        const { hash } = await writeContract({
          address: contractAddress,
          abi: tokenABI,
          functionName: 'claimTokens',
        })
        console.log(hash)
        this.showAlertBox('success', 'claiming tokens went successfull');

      } catch (error) {
        this.showAlertBox('error', 'claiming tokens failed');
        console.log(error)
      }
    }

  }
};
</script>

<style>
/* .cselect {
  appearance: none;
  padding: 8px;
  border: 1px solid #ccc;
  border-radius: 4px;
  font-size: 14px;
}

.option-image {
  width: 24px;
  height: 24px;
  margin-right: 8px;
} */


.errorAlert {
  background-color: rgb(255, 128, 128);
  display: none;
  position: fixed;
  top: 0;
  text-align: center;
  width: 100%;
  z-index: 2;
  font-size: 30px;
}

.successAlert {
  background-color: rgb(128, 255, 128);
  display: none;
  position: fixed;
  top: 0;
  text-align: center;
  width: 100%;
  z-index: 2;
  font-size: 30px;
}

.processAlert {
  background-color: rgb(255, 255, 128);
  display: none;
  position: fixed;
  top: 0;
  text-align: center;
  width: 100%;
  z-index: 2;
  font-size: 30px;
}
</style>