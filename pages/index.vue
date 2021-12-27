<template>
  <div class="h-screen w-full relative bg-image-app">
    <div v-if="showModal == true" @click.self="showModal = false" class="absolute top-0 left-0 w-full h-full bg-black opacity-50 fog"></div>
    <div class="w-full h-full flex flex-col items-center pt-6 relative z-20 px-4 lg:px-12">
      <div class="hidden absolute md:block top-0 left-0 mt-6 ml-6 z-10">
        <a target="_blank" href="https://discord.gg/crazydefenseheroes"><img src="~/assets/img/discord.png" class="h-12 w-12 mt-1 transform hover:scale-110" alt="discord icon"></a>
        <a target="_blank" href="https://twitter.com/TowerToken"><img src="~/assets/img/twitter.png" class="h-12 w-12 mt-1 transform hover:scale-110" alt="discord icon"></a>
        <a target="_blank" href="https://t.me/TowerToken"><img src="~/assets/img/telegram.png" class="h-12 w-12 mt-1 transform hover:scale-110" alt="discord icon"></a>
        <a target="_blank" href="https://medium.com/tower-token"><img src="~/assets/img/medium.png" class="h-12 w-12 mt-1 transform hover:scale-110" alt="discord icon"></a>
      </div>

      <div class="relative w-auto h-20 flex items-center justify-center">
        <img src="~/assets/img/banner.png" class="h-20 w-auto" alt="banner">
        <h1 class="absolute main-text pb-1">Crazy Defense Heroes</h1>
      </div>

      <div class="h-full w-full lg:h-2/3 lg:w-1/2 mt-4 flex flex-col items-center relative rounded-2xl pt-0 sm:pt-8 md:pt-2">
        <img src="~/assets/img/bg-card.jpg" class="hidden absolute lg:block w-full h-full bg-cover rounded-2xl" alt="background image">
        <img src="~/assets/img/bg-col.jpg" class="absolute lg:hidden bg-auto bg-cover rounded-2xl" alt="">
        <div class="w-full h-full px-12 py-4 flex flex-col items-center">
          <p v-if="textErr" class="text-red-400 lg:hidden text-xs text-center font-bold relative">{{textErr}}</p>
          <div class="max-w-xs w-full h-auto px-6 pt-4">
            <p class="text-black font-bold relative text-sm pb-2">current level:</p>
            <BaseSelect
              v-model="level"
              :data="xpTable"
              name="level"
              :err="levelErr"
              width="w-full"
              @dataSelected="setLevel"
            />
          </div>
            <div class="max-w-xs w-full h-auto px-6 pt-4">
              <p class="text-black font-bold relative text-sm pb-2">current XP level:</p>
              <div 
              :class="[
                currentXpInput ? 'main-border' : 'input-border',
                currentXpErr ? 'error-input' : '',
              ]" 
              class="w-full px-4 relative flex items-center">
                <input type="number" inputmode="numeric" class="w-full h-full z-10 py-2 text-black text-sm bg-transparent focus:outline-none" placeholder="000000" 
                v-model="levelXP"
                @focus="currentXpInput = true"
                @blur="currentXpInput = false">
              </div>
              <!-- <input type="text"> -->
            </div>
            <div class="max-w-xs w-full h-auto px-6 pt-4">
              <p class="text-black font-bold relative text-sm pb-2">XP to reach:</p>
              <div 
              :class="[
                xpGoalInput ? 'main-border' : 'input-border',
              ]" 
              class="w-full px-4 relative flex items-center">
                <input type="number" inputmode="numeric" class="w-full h-full z-10 py-2 text-black text-sm bg-transparent focus:outline-none" placeholder="000000" 
                v-model="xpNeeded"
                @focus="xpGoalInput = true"
                @blur="xpGoalInput = false">
              </div>
              <!-- <input type="text"> -->
            </div>
            <button class="relative submit text-sm px-12 py-2 font-semibold rounded-lg mt-8 opacity-75 hover:opacity-100" @click="calculate">Calculate</button>
            <p v-if="textErr" class="hidden lg:block text-red-400 text-xs pt-4 font-bold relative">{{textErr}}</p>
        </div>
      </div>
      <div class="md:hidden flex space-x-4 mb-4 z-10">
        <a href="https://discord.gg/crazydefenseheroes"><img src="~/assets/img/discord.png" class="h-12 w-12" alt="discord icon"></a>
        <a href="https://twitter.com/TowerToken"><img src="~/assets/img/twitter.png" class="h-12 w-12" alt="discord icon"></a>
        <a href="https://t.me/TowerToken"><img src="~/assets/img/telegram.png" class="h-12 w-12" alt="discord icon"></a>
        <a href="https://medium.com/tower-token"><img src="~/assets/img/medium.png" class="h-12 w-12" alt="discord icon"></a>
      </div>
    </div>
    <div
      :class="
        showModal ? '-translate-x-0 ease-out z-20' : 'translate-x-full ease-in'
      "
      @click.self="showModal = false"
      class="fixed right-0 top-0 max-w-sm w-full h-full px-6 py-4 transition duration-300 transform bg-auto overflow-y-auto top"
    >
      <div class="flex items-center justify-between relative">
        <h3 class="text-2xl font-medium text-white">Result</h3>
        <button class="text-white focus:outline-none" @click="showModal = !showModal">
          <svg
            class="h-5 w-5"
            fill="none"
            stroke-linecap="round"
            stroke-linejoin="round"
            stroke-width="2"
            viewBox="0 0 24 24"
            stroke="currentColor"
          >
            <path d="M6 18L18 6M6 6l12 12"></path>
          </svg>
        </button>
      </div>
      <div class="w-full flex flex-col space-y-2 pt-6">
        <p class="text-white">You have to reach level {{data.levelToReach}} and earn {{data.xpLeft}}XP on that level</p>
        <div class="flex items-center relative">
          <div class="w-full border py-1 rounded-md relative">
            <p class="text-white relative font-bold pl-3 top">
              Lvl. {{data.levelToReach}}
            </p>
            <div class="h-full progress-bar absolute top-0 rounded-md" :style="`width:${xpBar}`"></div>
          </div>
        </div>
        <p class="pb-1 pl-3 text-xs text-white font-semibold">
          {{progress}}
        </p>
        <p class="text-white text-sm">You have until {{date}}, to reach your goal level</p>
      </div>
    </div>
  </div>
</template>

<script>
  export default {
    data() {
      return {
        xpTable: [
          { "level": 1, "xp": 0 },
          { "level": 2, "xp": 1300 },
          { "level": 3, "xp": 2800 },
          { "level": 4, "xp": 5800 },
          { "level": 5, "xp": 10300 },
          { "level": 6, "xp": 16300 },
          { "level": 7, "xp": 23800 },
          { "level": 8, "xp": 32800 },
          { "level": 9, "xp": 43300 },
          { "level": 10, "xp": 55300 },
          { "level": 11, "xp": 68800 },
          { "level": 12, "xp": 83800 },
          { "level": 13, "xp": 100300 },
          { "level": 14, "xp": 118300 },
          { "level": 15, "xp": 137800 },
          { "level": 16, "xp": 158800 },
          { "level": 17, "xp": 181300 },
          { "level": 18, "xp": 205300 },
          { "level": 19, "xp": 230800 },
          { "level": 20, "xp": 257800 },
          { "level": 21, "xp": 286300 },
          { "level": 22, "xp": 316300 },
          { "level": 23, "xp": 347800 },
          { "level": 24, "xp": 380800 },
          { "level": 25, "xp": 415300 },
          { "level": 26, "xp": 451300 },
          { "level": 27, "xp": 491300 },
          { "level": 28, "xp": 535300 },
          { "level": 29, "xp": 583300 },
          { "level": 30, "xp": 635300 },
          { "level": 31, "xp": 691300 },
          { "level": 32, "xp": 751300 },
          { "level": 33, "xp": 815300 },
          { "level": 34, "xp": 883300 },
          { "level": 35, "xp": 955300 },
          { "level": 36, "xp": 1031300 },
          { "level": 37, "xp": 1111300 },
          { "level": 38, "xp": 1195300 },
          { "level": 39, "xp": 1283300 },
          { "level": 40, "xp": 1375300 },
          { "level": 41, "xp": 1471300 },
          { "level": 42, "xp": 1571300 },
          { "level": 43, "xp": 1675300 },
          { "level": 44, "xp": 1783300 },
          { "level": 45, "xp": 1895300 },
          { "level": 46, "xp": 2011300 },
          { "level": 47, "xp": 2131300 },
          { "level": 48, "xp": 2255300 },
          { "level": 49, "xp": 2383300 },
          { "level": 50, "xp": 2515300 },
          { "level": 51, "xp": 2651300 },
          { "level": 52, "xp": 2791300 },
          { "level": 53, "xp": 2935300 },
          { "level": 54, "xp": 3083300 },
          { "level": 55, "xp": 3235300 },
          { "level": 56, "xp": 3391300 },
          { "level": 57, "xp": 3551300 },
          { "level": 58, "xp": 3715300 },
          { "level": 59, "xp": 3883300 },
          { "level": 60, "xp": 4055300 },
          { "level": 61, "xp": 4231300 },
          { "level": 62, "xp": 4411300 },
          { "level": 63, "xp": 4595300 },
          { "level": 64, "xp": 4783300 },
          { "level": 65, "xp": 4975300 },
          { "level": 66, "xp": 5171300 },
          { "level": 67, "xp": 5371300 },
          { "level": 68, "xp": 5575300 },
          { "level": 69, "xp": 5783300 },
          { "level": 70, "xp": 5995300 },
          { "level": 71, "xp": 6211300 },
          { "level": 72, "xp": 6431300 },
          { "level": 73, "xp": 6655300 },
          { "level": 74, "xp": 6883300 },
          { "level": 75, "xp": 7115300 },
          { "level": 76, "xp": 7351300 },
          { "level": 77, "xp": 7591300 },
          { "level": 78, "xp": 7835300 },
          { "level": 79, "xp": 8083300 },
          { "level": 80, "xp": 8335300 },
          { "level": 81, "xp": 8591300 },
          { "level": 82, "xp": 8851300 },
          { "level": 83, "xp": 9115300 },
          { "level": 84, "xp": 9383300 },
          { "level": 85, "xp": 9655300 },
          { "level": 86, "xp": 9931300 },
          { "level": 87, "xp": 10211300 },
          { "level": 88, "xp": 10495300 },
          { "level": 89, "xp": 10783300 },
          { "level": 90, "xp": 11075300 },
          { "level": 91, "xp": 11371300 },
          { "level": 92, "xp": 11671300 },
          { "level": 93, "xp": 11975300 },
          { "level": 94, "xp": 12283300 },
          { "level": 95, "xp": 12595300 },
          { "level": 96, "xp": 12911300 },
          { "level": 97, "xp": 13231300 },
          { "level": 98, "xp": 13555300 },
          { "level": 99, "xp": 13883300 },
          { "level": 100, "xp": 14215300 },
          { "level": 101, "xp": 14551300 },
          { "level": 102, "xp": 14891300 },
          { "level": 103, "xp": 15235300 },
          { "level": 104, "xp": 15583300 },
          { "level": 105, "xp": 15935300 },
          { "level": 106, "xp": 16291300 },
          { "level": 107, "xp": 16651300 },
          { "level": 108, "xp": 17015300 },
          { "level": 109, "xp": 17383300 },
          { "level": 110, "xp": 17755300 },
          { "level": 111, "xp": 18131300 },
          { "level": 112, "xp": 18511300 },
          { "level": 113, "xp": 18895300 },
          { "level": 114, "xp": 19283300 },
          { "level": 115, "xp": 19675300 },
          { "level": 116, "xp": 20071300 },
          { "level": 117, "xp": 20471300 },
          { "level": 118, "xp": 20875300 },
          { "level": 119, "xp": 21283300 },
          { "level": 120, "xp": 21695300 },
          { "level": 121, "xp": 22111300 },
          { "level": 122, "xp": 22531300 },
          { "level": 123, "xp": 22955300 },
          { "level": 124, "xp": 23383300 },
          { "level": 125, "xp": 23815300 },
          { "level": 126, "xp": 24251300 },
          { "level": 127, "xp": 24691300 },
          { "level": 128, "xp": 25135300 },
          { "level": 129, "xp": 25583300 },
          { "level": 130, "xp": 26035300 },
          { "level": 131, "xp": 26491300 },
          { "level": 132, "xp": 26951300 },
          { "level": 133, "xp": 27415300 },
          { "level": 134, "xp": 27883300 },
          { "level": 135, "xp": 28355300 },
          { "level": 136, "xp": 28831300 },
          { "level": 137, "xp": 29311300 },
          { "level": 138, "xp": 29795300 },
          { "level": 139, "xp": 30283300 },
          { "level": 140, "xp": 30775300 },
          { "level": 141, "xp": 31271300 },
          { "level": 142, "xp": 31771300 },
          { "level": 143, "xp": 32275300 },
          { "level": 144, "xp": 32783300 },
          { "level": 145, "xp": 33295300 },
          { "level": 146, "xp": 33811300 },
          { "level": 147, "xp": 34331300 },
          { "level": 148, "xp": 34855300 },
          { "level": 149, "xp": 35383300 },
          { "level": 150, "xp": 35915300 },
	      ],
        months: [
          'January',
          'February',
          'March',
          'April',
          'May',
          'June',
          'July',
          'August',
          'September',
          'October',
          'November',
          'December',
        ],
        level: 1,
        levelXP: 0,
        levelErr: false,
        xpNeeded: 315000,
        showModal: false,
        currentXpInput: false,
        xpGoalInput: false,
        currentXpErr: false,
        textErr: '',
        data: {
          levelToReach: 1,
          levelToReachXp: 0,
          xpLeft: 0,
          nextLevelXp: 0,
        }
      }
    },
    computed: {
      progress() {
        return `${this.data.xpLeft} / ${this.data.nextLevelXp} exp`
      },
      xpBar() {
        const data = this.data;
        const totalXp = data.levelToReachXp + data.xpLeft;
        return ((totalXp - data.levelToReachXp) / data.nextLevelXp) * 100 + '%'
      },
      date() {
        // get the current month and last day of the month
        const currentMonth = new Date().getMonth();
        const lastDay = new Date(new Date().getFullYear(), currentMonth + 1, 0).getDate();
        const month = this.months[currentMonth];
        const date = `${month} ${lastDay}`;
        return date
      }
    },
    methods: {
      calculate() {
        const currentLevel = parseInt(this.level, 10) || 1;
        const levelXp = parseInt(this.levelXP, 10) || 0;
        const xpNeeded = parseInt(this.xpNeeded, 10) || 0;

        let currentLevelXP = this.xpTable[currentLevel-1].xp+levelXp;
        let nextLevel = this.xpTable[currentLevel];
        if (currentLevelXP >= nextLevel.xp) {
          this.textErr = 'Please make sure to add your current level XP';
          this.currentXpErr = true;
          setTimeout(() => {
            this.textErr = '';
            this.currentXpErr = false;
          }, 3000);
        } else {
          let totalXpToReach = currentLevelXP+xpNeeded;

          let findLevel = this.xpTable.find(x => x.xp > totalXpToReach);
          let goalLevel = findLevel.level-1;
          let xpLeft = totalXpToReach - this.xpTable[goalLevel-1].xp;
          let goalLevelXp = this.xpTable[goalLevel-1].xp
          const xpToReachNextLevel = findLevel.xp - goalLevelXp;

          this.showModal = true;
          this.setData(goalLevel, goalLevelXp, xpLeft, xpToReachNextLevel);   
        }
      },
      setData(levelToReach, levelToReachXp, xpLeft, nextLevelXp) {
        this.data = {
          levelToReach,
          levelToReachXp,
          xpLeft,
          nextLevelXp,
        }
      },
      setLevel(level) {
        this.level = level;
      },

    },
  }
</script>

<style scoped>
.main-text {
  font-size: 24px;
  font-weight: bold;
  color: #fff;
}
.fog {
  z-index: 998;
}
.top {
  z-index: 999;
}
.input-border {
  border-radius: 8px;
  border: 1px solid #949292;
}
.main-border {
  border: 1px solid #396afc;
  border-radius: 8px;
}
.error-input {
  border-color: #ff0000 !important;
}
.submit {
  background: #2330d0;
  color: #fff;
}
.progress-bar {
  background-image: linear-gradient(to right, #38e2e7, #5be5e9, #74e8eb, #88eaed, #9bedef);
}
.bg-image-app {
  background-image: url('~assets/img/bg-tower.jpg');
  background-size: cover;
  background-position: center;
  background-repeat: no-repeat;
  width: 100%;
  height: 100vh;
}
.error-input {
  border-color: #ff0000 !important;
}
</style>