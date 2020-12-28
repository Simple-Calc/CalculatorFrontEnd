<template>
  <!--<div>
    equation:{{ equation }};show:{{ show }};num1:{{ num1 }};num2:{{
      num2
    }};result:{{ result }};op:{{ op }}
  </div>-->
  <div class="box">
    <!--<div class="pastinput">{{ equation }}</div>-->
    <input class="pinput" v-model="equation" disabled="true" />
    <div class="currentinput">{{ show || result }}</div>
    <button
      class="greybtn"
      type="button"
      @click="percent(show ? show : result)"
    >
      %
    </button>
    <button class="greybtn" type="button" @click="clear()">CE</button>
    <button id="c" class="greybtn" type="button" @click="clear()">C</button>
    <button id="r" class="greybtn" type="button" @click="reverse()">R</button>
    <button
      class="greybtn"
      type="button"
      @click="inverse(show ? show : result)"
    >
      1/X
    </button>
    <button class="greybtn" type="button" @click="square(show ? show : result)">
      X^2
    </button>
    <button class="greybtn" type="button" @click="sqrt(show ? show : result)">
      sqrt
    </button>
    <button
      id="/"
      class="greybtn"
      type="button"
      @click="addoperation('divide')"
    >
      ÷
    </button>
    <button id="7" class="whitebtn" type="button" @click="adddigit('7')">
      7
    </button>
    <button id="8" class="whitebtn" type="button" @click="adddigit('8')">
      8
    </button>
    <button id="9" class="whitebtn" type="button" @click="adddigit('9')">
      9
    </button>
    <button
      id="*"
      class="greybtn"
      type="button"
      @click="addoperation('multiply')"
    >
      ×
    </button>
    <button id="4" class="whitebtn" type="button" @click="adddigit('4')">
      4
    </button>
    <button id="5" class="whitebtn" type="button" @click="adddigit('5')">
      5
    </button>
    <button id="6" class="whitebtn" type="button" @click="adddigit('6')">
      6
    </button>
    <button
      id="-"
      class="greybtn"
      type="button"
      @click="addoperation('subtract')"
    >
      -
    </button>
    <button id="1" class="whitebtn" type="button" @click="adddigit('1')">
      1
    </button>
    <button id="2" class="whitebtn" type="button" @click="adddigit('2')">
      2
    </button>
    <button id="3" class="whitebtn" type="button" @click="adddigit('3')">
      3
    </button>
    <button id="+" class="greybtn" type="button" @click="addoperation('add')">
      +
    </button>
    <button class="whitebtn" type="button" @click="inverteSign()">+/-</button>
    <button id="0" class="whitebtn" type="button" @click="adddigit('0')">
      0
    </button>
    <button id="." class="whitebtn" type="button" @click="adddot()">.</button>
    <button
      id="="
      class="equalbtn"
      type="button"
      @click="addoperation('equal')"
    >
      =
    </button>
  </div>
</template>

<script>
export default {
  name: "Calculator",
  data() {
    return {
      equation: "",
      show: "",
      num1: "0",
      num2: "0",
      result: "0",
      op: "",
      prevOp: "",
      checkFirst: true
    };
  },
  methods: {
    inverse(n) {
      this.calcIndividual("1", "divide", n);
    },
    square(n) {
      this.calcIndividual(n, "multiply", n);
    },
    sqrt(n) {
      this.calcIndividual(n, "sqrt", "2"); //2 is useless but not to conflict the program
    },
    percent(n) {
      this.calcIndividual(n, "divide", "100");
    },
    calcIndividual(n1, op, n2) {
      this.clear();
      this.equation = n1;
      this.show = "";
      this.setequation(op);
      this.equation += n2;
      this.num2 = n1;
      this.num1 = n2;
      this.equal(op);
    },
    adddigit(v) {
      if (this.op == "equal") this.clear();
      if (this.show.length < 16) {
        this.show == "0" ? (this.show = v) : (this.show += v);
        this.checkFirst ? (this.num2 = this.show) : (this.num1 = this.show);
      }
    },
    adddot() {
      if (this.show.length < 16 && !this.isFloat(this.show))
        this.show == "" ? (this.show += "0.") : (this.show += ".");
    },
    clear() {
      this.equation = "";
      this.show = "";
      this.num1 = "0";
      this.num2 = "0";
      this.result = "0";
      this.op = "";
      this.prevOp = "";
      this.checkFirst = true;
    },
    addoperation(op) {
      if (this.show != "") {
        this.prevOp = this.op;
        this.op = op;
        this.setequation(op);
        this.show = "";
        this.checkFirst = false;
        if (this.prevOp != "") this.equal(this.prevOp);
      } else {
        this.equation = this.equation.slice(0, this.show.length - 1);
        this.op = op;
        this.setequation(op);
      }
    },
    inverteSign() {
      this.show = parseFloat(this.show) * -1 + "";
    },
    reverse() {
      this.show = this.show.slice(0, this.show.length - 1);
      this.show == ("-" || "") ? (this.show = "0") : "";
      this.num1 = this.show;
    },

    equal(op) {
      fetch(
        "http://localhost:8085?f1=" +
          this.num2 +
          "&op=" +
          op +
          "&s2=" +
          this.num1
      )
        .then(response => {
          return response.text();
        })
        .then(data => {
          this.result = data;
          this.num2 = this.result;
        });
    },
    setequation(op) {
      switch (op) {
        case "add":
          this.equation += this.show + "+";
          break;
        case "subtract":
          this.equation += this.show + "-";
          break;
        case "multiply":
          this.equation += this.show + "×";
          break;
        case "divide":
          this.equation += this.show + "÷";
          break;
        case "equal":
          this.equation += this.show + "=";
          break;
      }
    },
    isFloat(n) {
      return n.indexOf(".") != "-1";
    }
  },
  created() {
    window.addEventListener("keydown", e => {
      switch (e.key) {
        case "0":
          document.getElementById("0").click();
          break;
        case "1":
          document.getElementById("1").click();
          break;
        case "2":
          document.getElementById("2").click();
          break;
        case "3":
          document.getElementById("3").click();
          break;
        case "4":
          document.getElementById("4").click();
          break;
        case "5":
          document.getElementById("5").click();
          break;
        case "6":
          document.getElementById("6").click();
          break;
        case "7":
          document.getElementById("7").click();
          break;
        case "8":
          document.getElementById("8").click();
          break;
        case "9":
          document.getElementById("9").click();
          break;
        case "+":
          document.getElementById("+").click();
          break;
        case "-":
          document.getElementById("-").click();
          break;
        case "*":
          document.getElementById("*").click();
          break;
        case "/":
          document.getElementById("/").click();
          break;
        case "=":
          document.getElementById("=").click();
          break;
        case "back-space":
          document.getElementById("r").click();
          break;
        case "c":
          document.getElementById("c").click();
          break;
        case ".":
          document.getElementById(".").click();
          break;
      }
    });
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
Calculator<style scoped lang="scss">
button {
  height: 50px;
  width: 70px;
  font-size: 20px;
}
/*.pastinput {
  height: 50px;
  width: 280px;
  font-size: 20px;
  background-color: rgb(202, 202, 202);
  text-align: end;
}*/
.pinput {
  height: 40px;
  width: 276px;
  font-size: 20px;
  border: none;
  background-color: rgb(202, 202, 202);
  text-align: end;
}
.currentinput {
  height: 60px;
  width: 280px;
  font-size: 30px;
  background-color: rgb(202, 202, 202);
  text-align: end;
}
.box {
  margin: auto;
  height: 400px;
  width: 280px;
  border: 3px solid rgb(161, 159, 159);
}
.equalbtn {
  background-color: rgb(79, 199, 230);
}
.whitebtn {
  background-color: white;
}
.greybtn {
  background-color: rgb(224, 224, 224);
}
</style>
