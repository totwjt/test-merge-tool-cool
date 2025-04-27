<template>
  <PageView
    v-model:page="state.pageConfig.page"
    v-model:size="state.pageConfig.size"
    :total="state.pageConfig.total"
    :showStatistic="true"
    @pageChange="onPage">
    <template #query>
      <AForm ref="queryFormRef" layout="inline" :model="queryModel" @submit="onQuery">
        <ARow :gutter="[0, 8]" class="query-rows">
          <ACol :xxl="4" :xl="5" :lg="5">
            <AFormItem label="姓名" name="name">
              <AInput
                v-model:value="queryModel.name"
                :disabled="selectState.open"
                :maxlength="15"
                allowClear
                placeholder="请输入医生姓名" />
            </AFormItem>
          </ACol>
          <ACol :xxl="4" :xl="5" :lg="5">
            <AFormItem label="手机号" name="phone">
              <extend-input
                v-model:value="queryModel.phone"
                :disabled="selectState.open"
                :maxlength="11"
                allowClear
                placeholder="请输入医生手机号" />
            </AFormItem>
          </ACol>
          <ACol :xxl="4" :xl="5" :lg="5">
            <AFormItem label="认证状态" name="state">
              <ASelect
                v-model:value="queryModel.state"
                :options="CertificationStatusList"
                :disabled="selectState.open"
                allowClear
                placeholder="请选择认证状态"></ASelect>
            </AFormItem>
          </ACol>
          <ACol :xxl="4" :xl="5" :lg="5">
            <AFormItem label="职称" name="occupationId">
              <ASelect
                v-model:value="queryModel.occupationId"
                :disabled="selectState.open"
                :options="JobTitleList"
                allowClear
                placeholder="请选择职称"></ASelect>
            </AFormItem>
          </ACol>
          <ACol :xxl="4" :xl="5" :lg="5">
            <AFormItem label="年龄" name="age">
              <extend-input
                v-model:value="queryModel.age"
                :disabled="selectState.open"
                type="type"
                :maxlength="2"
                allowClear
                placeholder="请输入医生年龄" />
            </AFormItem>
          </ACol>
          <ACol :xxl="4" :xl="5" :lg="5">
            <AFormItem label="医生ID" name="id">
              <extend-input
                v-model:value="queryModel.id"
                :disabled="selectState.open"
                :maxlength="20"
                allowClear
                placeholder="请输入医生ID" />
            </AFormItem>
          </ACol>
          <template v-if="state.collapse">
            <ACol :xxl="4" :xl="5" :lg="5">
              <AFormItem label="项目组" name="projectGroupId">
                <DoctorProjectGroupSelect
                  v-model:value="queryModel.projectGroupId"
                  :disabled="selectState.open"
                  :showSelect="roleType != 32 && roleType != 34"
                  :showTag="roleType === 32"
                  allowClear />
              </AFormItem>
            </ACol>
            <ACol :xxl="4" :xl="5" :lg="5">
              <AFormItem label="医生教育项目组" name="eduProjectGroupId">
                <DoctorEduProjectSelect
                  v-model:value="queryModel.eduProjectGroupId"
                  :disabled="selectState.open"
                  :showSelect="roleType != 34"
                  :showTag="roleType == 34"
                  allowClear />
              </AFormItem>
            </ACol>
            <ACol :xxl="4" :xl="5" :lg="5">
              <AFormItem label="渠道来源" name="createMode">
                <ASelect
                  v-model:value="queryModel.createMode"
                  :disabled="selectState.open"
                  :options="ChannelSource"
                  allowClear
                  placeholder="请选择渠道来源"></ASelect>
              </AFormItem>
            </ACol>
            <ACol :xxl="4" :xl="5" :lg="5">
              <AFormItem label="主职业单位" name="organiseId">
                <OrganiseQuerySelect
                  v-model:value="queryModel.organiseId"
                  v-model:name="queryModel.organiseName"
                  :disabled="selectState.open"
                  placeholder="请输入并选择"
                  allowClear />
              </AFormItem>
            </ACol>

            <ACol v-display-fields :xxl="4" :xl="5" :lg="5">
              <AFormItem label="科室">
                <DepartmentQuerySelect
                  v-model:value="queryModel.departmentId"
                  :disabled="selectState.open"
                  allowClear
                  placeholder="选择或搜索科室" />
              </AFormItem>
            </ACol>
            <ACol v-display-fields :xxl="4" :xl="5" :lg="5">
              <AFormItem label="关联医院">
                <InternetHospitalSelect
                  v-model:value="queryModel.internetHospitalId"
                  :disabled="selectState.open"
                  allowClear
                  placeholder="请选择关联医院" />
              </AFormItem>
            </ACol>
            <ACol :xxl="5" :xl="5" :lg="5">
              <AFormItem label="审核日期">
                <ExtendRangePicker
                  v-model:startTime="queryModel.examineBeginTime"
                  v-model:endTime="queryModel.examineEndTime"
                  :disabled="selectState.open"
                  allowClear />
              </AFormItem>
            </ACol>
            <ACol :xxl="5" :xl="5" :lg="5">
              <AFormItem label="创建日期">
                <ExtendRangePicker
                  v-model:startTime="queryModel.createBeginTime"
                  v-model:endTime="queryModel.createEndTime"
                  :disabled="selectState.open"
                  allowClear />
              </AFormItem>
            </ACol>
          </template>
          <ACol :xxl="4" :xl="5" :lg="5">
            <AFormItem>
              <ASpace>
                <AButton @click="onReset" :disabled="state.loading || selectState.open" type="dashed" html-type="button">重置</AButton>
                <AButton :loading="state.loading" :disabled="selectState.open" type="primary" html-type="submit">搜索</AButton>
                <AButton @click="addShow" v-auth="'addDoctor_2'" type="primary" ghost html-type="button">新增</AButton>
                <ToggleButton v-model:collapse="state.collapse" />
              </ASpace>
            </AFormItem>
          </ACol>
        </ARow>
      </AForm>
    </template>
    <template #tableHeader>
      <TableSelectButton
        v-model:open="selectState.open"
        :isAll="selectState.isAllSelect"
        :total="state.pageConfig.total"
        :selectCount="selectState.keys.length"
        :unSelectCount="selectState.unKeys.length"
        style="padding-bottom: 10px"
        @open="onSelectAllOpen"
        @selectAll="onSelectAllData"
        @submit="onSelectSubmit"
        @clear="onSelectAllClear" />
    </template>
    <template #table="{ width, height }">
      <ITable
        :loading="state.loading"
        :columns="columns"
        :dataSource="state.dataSource"
        :rowKey="record => record.id"
        :scroll="{ x: 1800, y: height }"
        :rowSelection="selectState.open ? {
          type: 'checkbox',
          fixed: true,
          preserveSelectedRowKeys: true,
          getCheckboxProps: (record) => ({ disabled: record.state !== 4 }),
          selectedRowKeys: selectState.keys,
          onSelectAll: onSelectAll,
          onSelect: onSelect
        } : null">
        <template #bodyCell="{ column, record, index }">

          <template v-if="column.dataIndex === 'name'">
            <a v-if="buttonShow['visiDoctor_2'] && record?.state === 4" @click="showSetting(record)">{{ record.name }}</a>
            <span v-else>{{ record.name }}</span>
          </template>

          <template v-if="column.dataIndex === 'goodLabelName'">
            <APopover>
              <template #content>
                <div style="max-width: 300px;">{{ record.goodLabelName }}</div>
              </template>
              <div class="text-overflow-1">{{ record.goodLabelName }}</div>
            </APopover>
          </template>
          <template v-if="column.dataIndex === 'phone'">
            {{ fn.phoneEncode(record?.phone) }}
          </template>
          <template v-if="column.dataIndex === 'createMode'">
            <ATag :color="CHANNEL_SOURCE_COLOR?.[record?.createMode]">
              {{ CHANNEL_SOURCE_DICT?.[record?.createMode] }}
            </ATag>
          </template>
          <template v-if="column.dataIndex === 'state'">
            <ATag :color="DOCTOR_AUTH_STATE_COLOR?.[record?.state]">
              {{ DOCTOR_AUTH_STATE_DICT?.[record?.state] }}
            </ATag>
          </template>
          <template v-if="column.dataIndex === 'action'">
            <ASpace>
              <!-- <AButton @click="detailShow(record, index)" v-auth="'doctorInfo_2'" type="link">详情</AButton> -->
              <AButton @click="detailShowNew(record, index)" v-auth="'doctorInfo_2'" type="link">详情</AButton>
              <AButton @click="editShow(record, index)" v-auth="'editDoctor_2'" type="link">编辑</AButton>
              <template v-if="record?.state == 4">
                <AButton @click="qrcodeShow(record, index)" v-auth="'doctorQr_2'" type="link">二维码</AButton>
              </template>
              <AButton @click="businessShow(record, index)" v-auth="'doctorBus_2'" type="link">业务管理</AButton>
              <AButton @click="assistantShow(record, index)" v-auth="'doctorAss_2'" type="link">医助管理</AButton>
              <template v-if="record?.state == 0">
                <AButton @click="onRemove(record, index)" v-auth="'delDoctor_2'" type="link">删除医生</AButton>
              </template>
              <template v-if="record?.idcard" >
              <AButton @click="protocolShow(record, index)" v-auth="'doctorAgreement_2'" type="link">协议</AButton>
              </template>
            </ASpace>
          </template>
        </template>
      </ITable>
    </template>

    <template #statistic>
      <StatisticRow :data="statisticData"></StatisticRow>
    </template>
    <template #footer>
      <a-button v-auth="'doctorExport_2'" type="primary" @click="exportExcel" ghost html-type="button">导出医生信息</a-button>
    </template>

    <!-- 医生设置 -->
     <DoctorSetting ref="doctorSettingRef" @coverClose="getData"></DoctorSetting>

    <!-- 新增/编辑弹窗 -->
    <DoctorAction ref="doctorActionRef" @refresh="getData" />

    <!-- 医生详情 - new -->
    <!-- <DoctorDetailNew ref="DoctorDetailNewRef" :buttonShow="buttonShow" @coverClose="detailCancel" @refresh="getData()" /> -->
    <DoctorDetailDrawer ref="doctorDetailDrawerRef" :isAuth="buttonShow.doctorAuth_2" @refresh="getData" />

    <!-- 二维码 -->
    <template v-if="qrcodeState.visible">
      <DoctorQRCode :fatherData="qrcodeState" @coverClose="qrcodeCancel" />
    </template>

    <!-- 业务管理组件 -->
    <template v-if="businessState.visible">
      <DoctorBusiness :fatherData="businessState" @coverClose="businessCancel" />
    </template>

    <!-- 医助管理 -->
    <template v-if="assistantState.visible">
      <DoctorAssistant :fatherData="assistantState" @coverClose="assistantCancel" />
    </template>

    <!-- 批量更改药房 -->
    <MultiBindPharmacy ref="multiBindPharmacyRef" @finished="onMultiBindPharmacyClose" />

    <!-- 医生协议管理 -->
    <protocolDoctor v-model:visible="protocolState.visible" :doctorInfo="protocolState.doctorInfo" />
  </PageView>
</template>

<script setup lang="ts">
import PageView, { ToggleButton } from '@/components/container/PageView'
import ExtendRangePicker from '@/components/funtional/ExtendRangePicker'
import DepartmentQuerySelect from '@/components/funtional/ExtendSelect/DepartmentQuerySelect'
import DoctorEduProjectSelect from '@/components/funtional/ExtendSelect/doctorEduProjectSelect'
import DoctorProjectGroupSelect from '@/components/funtional/ExtendSelect/doctorProjectGroupSelect'
import InternetHospitalSelect from '@/components/funtional/ExtendSelect/InternetHospitalSelect'
import ITable from '@/components/funtional/ITable'
import OrganiseQuerySelect from '@/components/funtional/querySelect/OrganiseQuerySelect.vue'
import StatisticRow from '@/components/public/StatisticRow.vue'
import DoctorAssistant from '@/components/userManage/doctor/assistantDoctor.vue'
import DoctorBusiness from '@/components/userManage/doctor/business.vue'
import { DoctorAction } from './components'
import type { DoctorActionInstance } from './components'
import DoctorQRCode from '@/components/userManage/doctor/QRCode.vue'
import { useDisplayColumns } from '@/hooks/functional/useDisplayColumns'
import { useExportExcel2 } from '@/hooks/functional/useExportExcel'
import {
  CHANNEL_SOURCE_COLOR,
  CHANNEL_SOURCE_DICT,
  CertificationStatusList,
  ChannelSource,
  DOCTOR_AUTH_STATE_COLOR,
  DOCTOR_AUTH_STATE_DICT,
  JobTitleList
} from '@/models/data/constants/dictList'
import fn from '@/models/ts/fn'
import { httpPost } from '@/plugins/axios'
import MultiBindPharmacy from './components/MultiBindPharmacy.vue'
import TableSelectButton from './components/TableSelectButton.vue'
import { DoctorDetailDrawer } from './components/DoctorDetail'
import type { DoctorDetailDrawerInstance } from './components/DoctorDetail'
import DoctorSetting from './doctorSetting/doctorSetting.vue'
import protocolDoctor from '@/components/userManage/doctor/protocolDoctor.vue'
import {
  useDoctorAction,
  useDoctorAssistant,
  useDoctorBusiness,
  useDoctorDetail,
  useDoctorQRCode,
  useDoctorRemove,
  useDoctorProtocol
} from './hooks'


const userInfo = storage.get('userInfo')?.user || {};
const roleType = userInfo?.roleType;
const buttonShow = fn._buttonShow('doctorManage_1');

const queryFormRef = ref();
const multiBindPharmacyRef = ref<InstanceType<typeof MultiBindPharmacy>>()
const doctorActionRef = ref<DoctorActionInstance>()
const doctorDetailDrawerRef = ref<DoctorDetailDrawerInstance>();
const state = reactive({
  collapse: false,
  loading: false,
  exportLoading: false,
  pageConfig: {
    page: 1,
    size: 20,
    total: 0,
    pageSizeOptions: () => ['10', '20', '30', '40', '50'],
  },
  dataSource: [],
  statisticData: [] as Record<string, any>
});
const selectState = reactive({
  open: false,
  keys: [],
  unKeys: [],
  isAllSelect: false,
})
const queryModel = ref({
  name: void 0,
  phone: void 0,
  state: void 0,
  occupationId: void 0,
  age: void 0,
  id: void 0,
  projectGroupId: void 0,
  eduProjectGroupId: void 0,
  createMode: void 0,
  organiseId: void 0,
  organiseName: void 0,
  examineBeginTime: void 0,
  examineEndTime: void 0,
  createBeginTime: void 0,
  createEndTime: void 0,
  departmentId: void 0,
  internetHospitalId: void 0
});

const columns = computed<ColumnType[]>(() => {
  const arr = [
    { title: '医生姓名', dataIndex: 'name', width: 140 },
    { title: '年龄', dataIndex: 'age', width: 80 },
    { title: '职称', dataIndex: 'occupationName', width: 140 },
    { title: '科室', dataIndex: 'departmentName', width: 140 },
    { title: '擅长', dataIndex: 'goodLabelName' },
    // { title: '加入医生教育项目组时间', dataIndex: 'joinTime', width: 180 },
    { title: '主职业单位', dataIndex: 'organiseName' },
    { title: '手机号', dataIndex: 'phone', width: 120 },
    { title: '渠道来源', dataIndex: 'createMode', width: 140 },
    { title: '认证状态', dataIndex: 'state', width: 140 },
    { title: '关联医院', dataIndex: 'internetHospitalName', width: 140 },
    { title: '操作', dataIndex: 'action', fixed: 'right', width: 340 }
  ];
  if (roleType == 34) {
    arr.splice(5, 0, { title: '加入医生教育项目组时间', dataIndex: 'joinTime', width: 180 });
  }

  return useDisplayColumns(arr,'internetHospitalName');
});
const statisticData = computed(() => {
  const data = state.statisticData || {};
  const type = data?.type ?? 1;  /** 这里取值与queryModel.value.type的取值相反 */
  return [
    {
      title: '科室总数',
      number: data?.departmentIdTotal ?? 0
    },
    {
      title: '医生总数',
      number: data?.doctorTotal ?? 0,
      add: type === 2 ? null : (data?.doctorTodayTotal ?? 0),
      desc: [
        {
          text: '主任',
          number: data?.primaryDoctorTotal ?? 0,
          add: type === 2 ? null : (data?.primaryDoctorTodayTotal ?? 0)
        },
        {
          text: '副主任',
          number: data?.assistantDoctorTotal ?? 0
        },
        {
          text: '主治',
          number: data?.majorDoctorTotal ?? 0,
          add: type === 2 ? null : (data?.majorDoctorTodayTotal ?? 0)
        }
      ]
    }
  ];
});


const { state: detailState, show: detailShow, cancel: detailCancel, DoctorDetailRef } = useDoctorDetail(getData);
const { addShow, editShow } = useDoctorAction(doctorActionRef, getData);
const { state: qrcodeState, show: qrcodeShow, cancel: qrcodeCancel } = useDoctorQRCode(getData);
const { state: businessState, show: businessShow, cancel: businessCancel } = useDoctorBusiness(getData);
const { state: assistantState, show: assistantShow, cancel: assistantCancel } = useDoctorAssistant(getData);
const { state: protocolState, show: protocolShow, cancel: protocolCancel } = useDoctorProtocol();
const { onRemove } = useDoctorRemove(getData);

onMounted(() => {
  onQuery();
});

function onQuery () {
  state.pageConfig.page = 1;
  getData();
}

function onPage() {
  getData();
}

function onReset () {
  state.pageConfig.page = 1;
  state.pageConfig.size = 20;

  queryModel.value.name = void 0;
  queryModel.value.phone = void 0;
  queryModel.value.state = void 0;
  queryModel.value.occupationId = void 0;
  queryModel.value.age = void 0;
  queryModel.value.id = void 0;
  queryModel.value.projectGroupId = void 0;
  queryModel.value.eduProjectGroupId = void 0;
  queryModel.value.createMode = void 0;
  queryModel.value.organiseId = void 0;
  queryModel.value.organiseName = void 0;
  queryModel.value.examineBeginTime = void 0;
  queryModel.value.examineEndTime = void 0;
  queryModel.value.createBeginTime = void 0;
  queryModel.value.createEndTime = void 0;
  queryModel.value.departmentId = void 0;
  queryModel.value.internetHospitalId = void 0;
  queryModel.value.type = 2;

  queryFormRef.value?.resetFields();

  selectState.open = false;
  selectState.isAllSelect = false;
  selectState.keys = [];
  selectState.unKeys = [];
  onQuery();
}

function getQueryParams () {
  const params: Record<string, any> = {...queryModel.value};

  /** 添加type字段 default: 2
   * type = 2: 没有搜索条件，查询全部数据
   * type = 1: 存在搜索条件
   * 用户统计接口统计昨日新增数
   */
  let num = 0;
  for (const key in params) {
    if (['name', 'phone', 'state', 'occupationId',
      'age', 'id', 'projectGroupId', 'eduProjectGroupId',
      'createMode', 'organiseId', 'organiseName', 'examineBeginTime',
      'examineEndTime', 'departmentId', 'internetHospitalId'].includes(key)) {
        // @ts-ignore
        num += !!params[key];
    }
  }
  params.type = num > 0 ? 1 : 2;

  params.curPage = state.pageConfig.page;
  params.pageSize = state.pageConfig.size;

  return params;
}

function getData () {
  if (state.loading) return;
  state.loading = true;
  getStatisticsData();
  const params = getQueryParams();
  httpPost(`/nld/doctor/queryDoctorList`, params).then(res => {
    const data = res?.data?.nldDoctorPage || {};
    state.dataSource = data?.records || [];
    state.pageConfig.page = data?.current ?? 1;
    state.pageConfig.size = data?.size ?? 20;
    state.pageConfig.total = data?.total ?? 0;

    // 如果是全选状态，则更新当前页数据的默认选中状态
    if (selectState.isAllSelect) {
      updateSelectKeys(
        true,
        state.dataSource.filter(item => item.state === 4 && !selectState.unKeys.includes(item.id)).map(item => item.id)
      )
    }
  }).finally(() => {
    state.loading = false;
  });
}

function getStatisticsData () {
  const params = getQueryParams();
  httpPost(`/nld/doctor/getDoctorStatistics`, params).then(res => {
    state.statisticData = res?.data || {};
  });
}

function exportExcel () {
  if (state.exportLoading) return;
  state.exportLoading = true;
  const params = getQueryParams();
  httpPost(`/nld/doctor/exportDoctor`, params, { responseType: 'blob' }).then(res => {
    useExportExcel2(res, res.response.headers);
  }).finally(() => {
    state.exportLoading = false;
  });
}

const doctorSettingRef = ref()
const showSetting = (record) => {

  const { id, docLabelState, openAi, faceVerification, name} = unref(record)

  doctorSettingRef.value?.show({
    id,
    docLabelState,
    openAi,
    faceVerification,
    name
  })

}

const DoctorDetailNewRef = ref()
const detailShowNew = (record, index) =>{
  // console.log('record, index', record, index);
  // const { id } = unref(record)
  // DoctorDetailNewRef.value?.show({
  //   doctorId: id
  // })
  doctorDetailDrawerRef.value?.show(record?.id);
}

/**
 * ==============================
 *  批量更改药房
 * ==============================
 */
/** 批量修改药房选择功能 启用/关闭钩子 */
function onSelectAllOpen(selected: boolean) {
  if (selected) {
    queryModel.value.state = 4
    onQuery()
  }
  selectState.isAllSelect = false
  selectState.keys = []
  selectState.unKeys = []
}

/**
 * 更新选择医生的keys字段
 * @param {} selected - 是否选择; 当为true时，`selectState.keys`添加选择项; 当为false时，`selectState.keys`移出选择项
 */
function updateSelectKeys(selected: boolean, keys: string | string[]) {
  if (!keys?.length) return

  if (selected) {
    if (Array.isArray(keys)) {
      keys.forEach(item => {
        if (!selectState.keys.some(k => k === item)) {
          selectState.keys.push(item)
        }
      })
    } else {
      if (!selectState.keys.some(k => k === keys)) {
        selectState.keys.push(keys)
      }
    }
  } else {
    if (Array.isArray(keys)) {
      selectState.keys = selectState.keys.filter(item => !keys.includes(item))
    } else {
      selectState.keys = selectState.keys.filter(item => item !== keys)
    }
  }
}

/**
 * 更新取消选择医生的keys字段
 * @param {} selected - 是否选择; 当为true时，`selectState.unKeys`移出选择项; 当为false时，`selectState.unKeys`添加选择项
 */
function updateSelectUnKeys(selected: boolean, keys: string | string[]) {
  if (!keys?.length) return

  if (selected) {
    if (Array.isArray(keys)) {
      selectState.unKeys = selectState.unKeys.filter(item => !keys.includes(item))
    } else {
      selectState.unKeys = selectState.unKeys.filter(item => item !== keys)
    }
  } else {
    if (Array.isArray(keys)) {
      keys.forEach(item => {
        if (!selectState.unKeys.some(k => k === item)) {
          selectState.unKeys.push(item)
        }
      })
    } else {
      if (!selectState.unKeys.some(k => k === keys)) {
        selectState.unKeys.push(keys)
      }
    }
  }
}

/** 表格全选/取消全选 */
function onSelectAll(selected: boolean, selectedRows: Array<Record<string, any>>, changeRows: Array<Record<string, any>>) {
  const ids = changeRows.filter(item => item && item?.state === 4).map(item => item.id)
  if (selectState.isAllSelect) {
    // 全选按钮状态 开启
    updateSelectKeys(selected, ids)
    updateSelectUnKeys(selected, ids)
  } else {
    // 全选按钮状态 关闭
    updateSelectKeys(selected, ids)
    updateSelectUnKeys(selected, ids)
  }
}

/** 表格手动选择 */
function onSelect(record: Record<string, any>, selected: boolean, selectedRows: Array<Record<string, any>>) {
  if (!record || record?.state !== 4) return;

  if (selectState.isAllSelect) {
    // 全选按钮状态 开启
    updateSelectKeys(selected, record.id)
    updateSelectUnKeys(selected, record.id)
  } else {
    // 全选按钮状态 关闭
    updateSelectKeys(selected, record.id)
    updateSelectKeys(selected, record.id)
  }
}

/** 全选/取消全选 全部医生(数据层面) */
function onSelectAllData(selected: boolean) {
  selectState.isAllSelect = selected
  selectState.keys = []
  selectState.unKeys = []

  if(selected) {
    updateSelectKeys(selected, state.dataSource.filter(item => item.state === 4).map(item => item.id))
  }
}

/** 清空选择全部医生(数据层面) */
function onSelectAllClear() {
  onSelectAllData(false)
}

function onSelectSubmit() {
  const doctorIds = selectState.isAllSelect ? (selectState.unKeys || []) : (selectState.keys || []);
  const queryParams = getQueryParams();
  multiBindPharmacyRef.value?.show(selectState.isAllSelect, doctorIds, queryParams)
}

function onMultiBindPharmacyClose () {
  selectState.open = false;
  selectState.keys = []
  selectState.unKeys = []
  selectState.isAllSelect = false
  getData()
}
</script>

<style scoped lang="scss">
</style>
