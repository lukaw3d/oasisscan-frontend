<template>
  <div v-if="type==='simple'" :class="rootClasses">
    <div class="sim-btn" @click="first">
      <img src="../assets/first.svg"/>
    </div>
    <div class="sim-btn" @click="previous">
      <img src="../assets/pre.svg"/>
    </div>
    <span class="sim-text">Page {{page}} of {{total}}</span>
    <div class="sim-btn" @click="next">
      <img src="../assets/next.svg"/>
    </div>
    <div class="sim-btn" @click="last">
      <img src="../assets/latest.svg" />
    </div>
  </div>
  <div v-else :class="rootClasses">
    <span class="sizer">{{$t('pageSize', {sizer: sizer})}}</span>
    <button class="previous" @click="previous">
      <Icon type="md-arrow-dropleft" class="previous-arrow" />
    </button>
    <ul class="pages-list">
      <li v-for="(item, index) in pages" :key="item + '-' + index" :class="[item === '...' ? 'dots' : 'page-number', item === page ? 'current' : '']" @click="pageNumberClick(item)">
        <span> {{ item }}</span>
      </li>
    </ul>
    <button class="next" @click="next">
      <Icon type="md-arrow-dropright" class="next-arrow" />
    </button>
    <form @submit.prevent.stop="to" class="to-page-wrapper">
      <span v-for="(text, textIndex) in toPageText" :key="textIndex">
        <input v-if="text === '$'" type="text" v-model="toPage"/>
        <span v-else>{{text}}</span>
      </span>
    </form>
  </div>
</template>

<script>
  import cls from 'classnames';
  export default {
    props: {
      type: String,
      rootClass: String,
      sizer: Number,
      page: Number,
      recordsCount: Number
    },
    name: 'Page',
    data() {
      return {
        rootClasses: '',
        toPage: ''
      }
    },
    created() {
      this.rootClasses = cls('page-wrapper', this.rootClass)
    },
    methods: {
      first() {
        // console.log('ffff')
        this.$emit('goto', 1)
      },
      last() {
        this.$emit('goto', this.total)
      },
      previous() {
        if (this.page <= 1) {
          return
        }
        this.$emit('goto', this.page - 1)
      },
      next() {
        if (this.page >= this.total) {
          return
        }
        this.$emit('goto', this.page + 1)
      },
      pageNumberClick(number) {
        if (number === '...') {
          return
        }
        if (number !== this.page) {
          this.$emit('goto', number)
        }
      },
      to(e) {
        if (String(this.toPage).match(/^\d+$/)) {
          const pageNumber = parseInt(this.toPage)
          if (pageNumber > 0 && pageNumber <= this.total && pageNumber !== this.page) {
            this.$emit('goto', parseInt(this.toPage))
          }
        }
      }
    },
    computed: {
      toPageText() {
        const arr = []
        const text = this.$t('topage', { pageNumber: '$' })
        const inputIndex = text.indexOf('$')
        arr.push(text.substring(0, inputIndex))
        arr.push('$')
        arr.push(text.substring(inputIndex + 1, text.length))
        return arr
      },
      total() {
        return Math.ceil(this.recordsCount / this.sizer)
      },
      pages() {
        const pageArray = []
        const total = this.total
        const min = Math.max(this.page - 2, 1)
        const max = Math.min(this.page + 2, total)
        const dots = '...'
        for (let i = min; i <= max; i++) {
          pageArray.push(i)
        }
        if (min > 2) {
          pageArray.unshift(dots)
        }
        if (min > 1) {
          pageArray.unshift(1)
        }
        if (max < total - 1) {
          pageArray.push(dots)
        }
        if (max <= total - 1) {
          pageArray.push(total)
        }
        return pageArray
      }
    }
  }
</script>

<style scoped lang="scss">
  @import "../assets/css/common";
  .sim-btn {
    width: rem(40);
    height: rem(24);
    display: flex;
    align-items: center;
    justify-content: center;
    cursor: pointer;
    margin-left: rem(9);
    &:first-child {
      margin-left: rem(0);
    }
    img {
      height: rem(24);
    }
  }
  .sim-text {
    color: #979797;
    font-size: rem(12);
    margin-left: rem(9);
  }
  .page-wrapper {
    display: flex;
    align-items: center;
  }
  .previous,.next {
    width:20px;
    height:20px;
    border-radius:2px;
    border:1px solid rgba(55,65,107,0.1);
    cursor: pointer;
    background-color: white;
    &:focus {
      outline: none;
    }
    .previous-arrow,.next-arrow {
      font-size: 20px;
    }
  }
  .previous {
    margin-right: 10px;
  }
  .next {
    margin-left: 10px;
  }
  .sizer {
    margin-right: 20px;
    font-size:12px;
    font-family:PingFangSC-Regular;
    font-weight:400;
    color:rgba(55,65,107,0.5);
    line-height: 1;
  }
  .pages-list {
    display: flex;
    list-style: none;
    li {
      font-size:14px;
      font-family:PingFangSC-Medium;
      font-weight:500;
      color:rgba(55,65,107,1);
      line-height: 1;
      padding: 10px;
      &.page-number{
        cursor: pointer;
      }
      &.current {
        text-decoration: underline;
      }
    }
  }
  .to-page-wrapper {
    margin-left: 20px;
    font-size:12px;
    font-family:PingFangSC-Regular;
    font-weight:400;
    color:rgba(55,65,107,0.5);
    line-height: 1;
    display: flex;
    align-items: center;
    input {
      width:20px;
      height:20px;
      border-radius:2px;
      border:1px solid rgba(55,65,107,0.1);
      margin: 0 0 0 10px;
      text-align: center;
      display: flex;
      align-items: center;
      &:focus {
        outline: none;
      }
    }
  }
</style>
