<template>
  <div id="compliance-table" class="compliance-table-viewer">

    <div class="viewer-filter">

      <div class="form-group">
        <label for="exampleFormControlInput1">단계 필터</label>
        <div class="form-check" v-for="n in depth" :key="n">
          <input class="form-check-input" type="checkbox"
                 :value="n"
                 :checked="depthVisible.indexOf(n) >= 0"
                 @click="onClickDepthVisibleCheckbox($event, n)">
          <label class="form-check-label" v-text="n + ' 단계'"></label>
        </div>
      </div>

    </div>
    <div class="viewer-content">

      <table class="table text-left table-bordered">
        <tr v-for="row in complianceRows" :key="row[depth - 1].id">
          <template v-for="(comp, index) in row">
            <td v-if="comp != null && depthVisible.indexOf(index + 1) >= 0"
                :key="comp.id"
                :rowspan="childrenCount[comp.number]">
              <span class="badge badge-pill badge-secondary" v-text="comp.number"></span> <br>
              <span v-text="index === depth - 1 ? comp.content : comp.title"></span>
            </td>
          </template>
        </tr>
      </table>

    </div>

  </div>
</template>

<script>
import _ from "lodash";

export default {
  name: "ComplianceTable",
  mounted() {
    this.complianceCollection = getComplianceList()
    this.parseComplianceCollection(this.complianceCollection);
  },
  data() {
    return {
      complianceCollection: [],
      complianceGroupedByDepth: {},
      depth: 0,
      childrenCount: {},
      complianceRows: [],
      depthVisible: [],
    }
  },
  methods: {
    onClickDepthVisibleCheckbox: function (event, depth) {
      if (event.target.checked) {
        this.depthVisible.push(depth)
      } else {
        this.depthVisible.splice(this.depthVisible.indexOf(depth), 1)
      }
    },
    parseComplianceCollection: function (collection) {

      if (collection.length < 1) return;

      let maxDotCount = 0;
      const complianceGroupedByDepth = _.groupBy(collection, (compliance) => {
        const dotCount = (compliance.number.match(/\./g) || []).length
        if (dotCount > maxDotCount) maxDotCount = dotCount;
        return dotCount;
      })

      const depth = this.depth = maxDotCount + 1;
      this.complianceGroupedByDepth = complianceGroupedByDepth;

      const childrenCount = {};
      complianceGroupedByDepth[maxDotCount].forEach((compliance) => {
        const splitNumber = compliance.number.split(".");

        if (splitNumber.length !== 1) {
          let numberPieces = "";
          for(let i=0; i<splitNumber.length -1; i++) {
            if (numberPieces !== "") numberPieces += "."
            numberPieces += splitNumber[i];

            if (childrenCount[numberPieces] === undefined) {
              childrenCount[numberPieces] = 0;
            }
            childrenCount[numberPieces]++;
          }
        }
      })

      this.childrenCount = childrenCount;

      const itemXDepthRows = [];
      for (let d = 0; d < maxDotCount + 1; d++) {
        const row = [];
        for (let i = 0; i < complianceGroupedByDepth[d].length; i++) {
          const compliance = complianceGroupedByDepth[d][i];
          let childrenCountOfCompliance = childrenCount[compliance.number];
          if (d === maxDotCount) {
            childrenCountOfCompliance = 1;
          }
          for (let x = 0; x < childrenCountOfCompliance; x++) {
            if (x === 0) {
              row.push(compliance)
            } else {
              row.push(null)
            }
          }
        }
        itemXDepthRows.push(row);
      }

      const depthXItemRows = [];
      for (let i = 0; i < complianceGroupedByDepth[maxDotCount].length; i++) {
        const row = [];
        for (let d = 0; d < depth; d++) {
          row.push(itemXDepthRows[d][i]);
        }
        depthXItemRows.push(row);
      }

      this.complianceRows = depthXItemRows;

      this.depthVisible = Array(depth).fill(1).map((x, y) => x + y);

    }
  }
}

function getComplianceList() {
  return [
    {
      id: "97",
      number: "1",
      title: "관리체계 수립 및 운영",
      content: "",
      description: "",
      task: "",
      parent_id: "",
    },
    {
      id: "98",
      number: "2",
      title: "보호대책 요구사항",
      content: "",
      description: "",
      task: "",
      parent_id: "",
    },
    {
      id: "99",
      number: "1.1",
      title: "관리체계 기반 마련",
      content: "",
      description: "",
      task: "",
      parent_id: "97",
    },
    {
      id: "100",
      number: "1.2",
      title: "위험 관리",
      content: "",
      description: "",
      task: "",
      parent_id: "97",
    },
    {
      id: "101",
      number: "2.1",
      title: "정책, 조직, 자산 관리",
      content: "",
      description: "",
      task: "",
      parent_id: "98",
    },
    {
      id: "102",
      number: "2.2",
      title: "개인정보 보유 및 이용 시 보호조치",
      content: "",
      description: "",
      task: "",
      parent_id: "98",
    },
    {
      id: "103",
      number: "1.1.1",
      title: "경영진의 참여",
      content: "",
      description: "최고경영자는 정보보호 및 개인정보보호 관리체계의 관리체게의 수립과 운영활동 전반에 경영진의 참여가 이루어질 수 있도록 보고 및 의사결정 체계를 수립하여 운영하여야 한다.",
      task: "",
      parent_id: "99",
    },
    {
      id: "104",
      number: "1.1.2",
      title: "최고책임자의 지정",
      content: "",
      description: "최고경영자는 정보보호 업무를 총괄하는 정보보호 최고책임자와 개인정보보호 업무를 총괄하는 개인정보보호 책임자를 예산·인력 등 자원을 할당할 수 있는 임원급으로 지정하여야 한다.",
      task: "",
      parent_id: "99",
    },
    {
      id: "105",
      number: "1.2.1",
      title: "정보자산 식별",
      content: "",
      description: "조직의 업무특성에 따라 정보자산 분류기준을 수립하여 관리체계 범위 내 모든 정보자산을 식별·분류하고, 중요도를 산정한 후 그 목록을 최신으로 관리하여야 한다.",
      task: "",
      parent_id: "100",
    },
    {
      id: "106",
      number: "2.1.1",
      title: "정책의 유지관리",
      content: "",
      description: "정보보호 및 개인정보보호 관련 정책과 시행문서는 법령 및 규제, 상위 조직 및 관련 기관 정책과의 연계성, 조직의 대내외 환경변화 등에 따라 주기적으로 검토하여 필요한 경우 제∙개정하고 그 내역을 이력관리하여야 한다.",
      task: "",
      parent_id: "101",
    },
    {
      id: "107",
      number: "2.1.2",
      title: "조직의 유지관리",
      content: "",
      description: "조직의 각 구성원에게 정보보호와 개인정보보호 관련 역할 및 책임을 할당하고, 그 활동을 평가할 수 있는 체계와 조직 및 조직의 구성원 간 상호 의사소통할 수 있는 체계를 수립하여 운영하여야 한다.",
      task: "",
      parent_id: "101",
    },
    {
      id: "108",
      number: "2.2.1",
      title: "개인정보 현황관리",
      content: "",
      description: "수집·보유하는 개인정보의 항목, 보유량, 처리 목적 및 방법, 보유기간 등 현황을 정기적으로 관리하여야 하며, 공공기관의 경우 이를 법률에서 정한 관계기관의 장에게 등록하여야 한다.",
      task: "",
      parent_id: "102",
    },
    {
      id: "109",
      number: "1.1.1.1",
      title: "",
      content: "정보보호 및 개인정보보호 관리체계의 수립 및 운영활동 전반에 경영진의 참여가 이루어질 수 있도록 보고 및 의사결정 등의 책임과 역할을 문서화하고 있는가?",
      description: "",
      task: "y",
      parent_id: "103",
    },
    {
      id: "110",
      number: "1.1.2.1",
      title: "",
      content: "최고경영자는 정보보호 및 개인정보보호 처리에 관한 업무를 총괄하여 책임질 최고책임자를 공식적으로 지정하고 있는가?",
      description: "",
      task: "y",
      parent_id: "104",
    },
    {
      id: "111",
      number: "1.1.2.2",
      title: "",
      content: "정보보호 최고책임자 및 개인정보 보호책임자는 예산, 인력 등 자원을 할당할 수 있는 임원급으로 지정하고 있으며 관련 법령에 따른 자격요건을 충족하고 있는가?",
      description: "",
      task: "y",
      parent_id: "104",
    },
    {
      id: "112",
      number: "1.2.1.1",
      title: "",
      content: "정보자산의 분류기준을 수립하고 정보보호 및 개인정보보호 관리체계 범위 내의 모든 자산을 식별하여 목록으로 관리하고 있는가?",
      description: "",
      task: "y",
      parent_id: "105",
    },
    {
      id: "113",
      number: "1.2.1.2",
      title: "",
      content: "관리체계 범위 내 개인정보 처리 현황을 식별하고 개인정보의 흐름을 파악하여 개인정보흐름도 등으로 문서화하고 있는가?",
      description: "",
      task: "y",
      parent_id: "105",
    },
    {
      id: "114",
      number: "1.2.1.3",
      title: "",
      content: "조직에서 수용 가능한 목표 위험수준을 정하고 그 수준을 초과하는 위험을 식별하고 있는가?",
      description: "",
      task: "y",
      parent_id: "105",
    },
    {
      id: "115",
      number: "2.1.1.1",
      title: "",
      content: "정보보호 및 개인정보보호 관련 정책 및 시행문서에 대한 정기적인 타당성 검토 절차를 수립‧이행하고 있는가?",
      description: "",
      task: "y",
      parent_id: "106",
    },
    {
      id: "116",
      number: "2.1.2.1",
      title: "",
      content: "정보보호 및 개인정보보호 관련 책임자와 담당자의 역할 및 책임을 명확히 정의하고 있는가?",
      description: "",
      task: "y",
      parent_id: "107",
    },
    {
      id: "117",
      number: "2.1.2.2",
      title: "",
      content: "법적 근거에 따라 정보주체(이용자)의 주민등록번호 수집이 가능한 경우에도 아이핀, 휴대폰 인증 등 주민등록번호를 대체하는 수단을 제공하고 있는가?",
      description: "",
      task: "y",
      parent_id: "107",
    },
    {
      id: "118",
      number: "2.2.1.1",
      title: "",
      content: "수집·보유하고 있는 개인정보의 항목, 보유량, 처리 목적 및 방법, 보유기간 등 현황을 정기적으로 관리하고 있는가?",
      description: "",
      task: "y",
      parent_id: "108",
    },
    {
      id: "119",
      number: "2.2.1.2",
      title: "",
      content: "공공기관이 개인정보파일을 운용하거나 변경하는 경우 관련된 사항을 법률에서 정한 관계기관의 장에게 등록하고 있는가?",
      description: "",
      task: "y",
      parent_id: "108",
    },
    {
      id: "120",
      number: "2.2.1.3",
      title: "",
      content: "공공기관은 개인정보파일의 보유 현황을 개인정보 처리방침에 공개하고 있는가?",
      description: "",
      task: "y",
      parent_id: "108",
    },
  ]
}
</script>

<style scoped>
  .compliance-table-viewer {
    padding-left: 240px;
  }
  .viewer-filter {
    position: fixed;
    top: 0;
    left: 0;
    width: 240px;
    height: 100%;
    border-right: 1px solid #ccc;
    padding: 1rem;
    text-align: left;
    background-color: #fff;
  }
  .viewer-content {
    padding: 1rem;
  }
  .viewer-content .table {
    background-color: #fff;
  }
</style>