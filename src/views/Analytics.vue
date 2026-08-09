<template>
  <div class="space-y-8 font-sans pb-12">
    <!-- Top Header Banner -->
    <div class="p-6 rounded-2xl bg-gradient-to-l from-slate-900 via-slate-900 to-primary-950/40 border border-slate-800 flex flex-col lg:flex-row lg:items-center justify-between gap-4">
      <div>
        <div class="flex items-center gap-2 mb-1">
          <span class="px-2.5 py-0.5 rounded-full text-xs font-bold bg-primary-500/10 text-primary-400 border border-primary-500/20">
            مركز التحليلات والمؤشرات
          </span>
          <span class="text-slate-500 text-xs">• تحديث حي ومباشر</span>
        </div>
        <h2 class="text-2xl font-black text-white">الإحصائيات والتحليلات الشاملة للتطبيق</h2>
        <p class="text-slate-400 text-sm mt-1">
          رؤية دقيقة لمشاركة الزوار حسب المحافظات، منجزي الحقوق الـ 50 بالكامل، وإحصائيات الإجابات الصحيحة والخاطئة.
        </p>
      </div>

      <div class="flex items-center gap-3">
        <button
          @click="loadAllData"
          :disabled="loading"
          class="flex items-center gap-2 px-4 py-2.5 rounded-xl bg-slate-900 border border-slate-800 text-slate-300 hover:text-white hover:bg-slate-800 font-semibold text-xs transition disabled:opacity-50"
        >
          <RefreshCw :class="['w-4 h-4 text-primary-400', loading ? 'animate-spin' : '']" />
          <span>تحديث البيانات</span>
        </button>

        <button
          @click="triggerExcelExport"
          :disabled="loading || usersList.length === 0"
          class="flex items-center gap-2 px-4 py-2.5 rounded-xl bg-emerald-600 text-white font-bold text-xs shadow-lg shadow-emerald-600/20 hover:bg-emerald-500 transition-all hover:-translate-y-0.5 disabled:opacity-50 disabled:pointer-events-none"
        >
          <FileSpreadsheet class="w-4 h-4" />
          <span>تصدير تقرير Excel</span>
        </button>
      </div>
    </div>

    <!-- Main KPI Cards Grid -->
    <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-6">
      <!-- Card 1: Total Users & Active -->
      <div class="glass-panel p-6 rounded-2xl relative overflow-hidden group">
        <div class="flex items-center justify-between">
          <div>
            <p class="text-slate-400 text-xs font-bold mb-1">إجمالي الزوار المسجلين</p>
            <h3 class="text-3xl font-black text-white">{{ stats.totalUsers }}</h3>
          </div>
          <div class="p-3 bg-emerald-600/10 text-emerald-400 rounded-xl group-hover:scale-110 transition-transform">
            <Users class="w-6 h-6" />
          </div>
        </div>
        <div class="mt-4 pt-3 border-t border-slate-800/60 flex items-center justify-between text-xs">
          <span class="text-slate-400 flex items-center gap-1.5">
            <span class="w-2 h-2 rounded-full bg-emerald-500 animate-pulse"></span>
            <span>{{ stats.activeUsers }} نشط حالياً</span>
          </span>
          <span class="text-slate-500">{{ stats.totalGovernorates }} محافظة مشاركة</span>
        </div>
      </div>

      <!-- Card 2: 50 Rights Achievers -->
      <div class="glass-panel p-6 rounded-2xl relative overflow-hidden group border-amber-500/20 bg-gradient-to-b from-amber-500/5 to-transparent">
        <div class="flex items-center justify-between">
          <div>
            <p class="text-amber-400/90 text-xs font-bold mb-1">منجزو الحقوق الـ 50 بالكامل</p>
            <h3 class="text-3xl font-black text-amber-300">{{ stats.completed50Users }} <span class="text-sm font-normal text-amber-400/70">أفراد</span></h3>
          </div>
          <div class="p-3 bg-amber-500/10 text-amber-400 rounded-xl group-hover:scale-110 transition-transform">
            <Award class="w-6 h-6" />
          </div>
        </div>
        <div class="mt-4 pt-3 border-t border-amber-500/10 flex items-center justify-between text-xs">
          <span class="text-amber-300/80 font-semibold">{{ stats.completed50Percentage }}% من إجمالي الزوار</span>
          <span class="text-slate-400">🏅 ختم 50/50</span>
        </div>
      </div>

      <!-- Card 3: Total Quiz Submissions -->
      <div class="glass-panel p-6 rounded-2xl relative overflow-hidden group">
        <div class="flex items-center justify-between">
          <div>
            <p class="text-slate-400 text-xs font-bold mb-1">مشاركات الاختبارات والأسئلة</p>
            <h3 class="text-3xl font-black text-white">{{ stats.totalQuizAttempts }}</h3>
          </div>
          <div class="p-3 bg-indigo-600/10 text-indigo-400 rounded-xl group-hover:scale-110 transition-transform">
            <HelpCircle class="w-6 h-6" />
          </div>
        </div>
        <div class="mt-4 pt-3 border-t border-slate-800/60 flex items-center justify-between text-xs">
          <span class="text-slate-400">متوسط الإجابة لكل زائر:</span>
          <span class="text-indigo-400 font-bold">{{ stats.avgQuizPerUser }} أسئلة</span>
        </div>
      </div>

      <!-- Card 4: Correct Answers & Accuracy -->
      <div class="glass-panel p-6 rounded-2xl relative overflow-hidden group">
        <div class="flex items-center justify-between">
          <div>
            <p class="text-slate-400 text-xs font-bold mb-1">معدل الإجابات الصحيحة</p>
            <h3 class="text-3xl font-black text-emerald-400">{{ stats.accuracyRate }}%</h3>
          </div>
          <div class="p-3 bg-emerald-600/10 text-emerald-400 rounded-xl group-hover:scale-110 transition-transform">
            <CheckCircle2 class="w-6 h-6" />
          </div>
        </div>
        <div class="mt-4 pt-3 border-t border-slate-800/60 flex items-center justify-between text-xs">
          <span class="text-emerald-400 font-bold">✓ {{ stats.correctAnswersCount }} صحيحة</span>
          <span class="text-rose-400 font-bold">✗ {{ stats.wrongAnswersCount }} خاطئة</span>
        </div>
      </div>
    </div>

    <!-- Tab Navigation & Filters -->
    <div class="flex flex-wrap items-center justify-between gap-4 border-b border-slate-800 pb-4">
      <div class="flex items-center gap-2 overflow-x-auto pb-1 max-w-full">
        <button
          v-for="tab in tabs"
          :key="tab.id"
          @click="activeTab = tab.id"
          :class="[
            'flex items-center gap-2 px-4 py-2.5 rounded-xl font-bold text-xs transition-all whitespace-nowrap',
            activeTab === tab.id
              ? 'bg-primary-600 text-white shadow-lg shadow-primary-600/20'
              : 'bg-slate-900 border border-slate-800 text-slate-400 hover:text-white hover:bg-slate-800'
          ]"
        >
          <component :is="tab.icon" class="w-4 h-4" />
          <span>{{ tab.name }}</span>
        </button>
      </div>

      <div class="flex items-center gap-2">
        <span class="text-xs text-slate-500">طريقة العرض:</span>
        <span class="px-3 py-1 rounded-lg bg-slate-900 border border-slate-800 text-xs font-semibold text-slate-300">
          تحليل تفاعلي متقدم
        </span>
      </div>
    </div>

    <!-- TAB 1: GEOGRAPHICAL ANALYTICS -->
    <div v-if="activeTab === 'geo'" class="space-y-8 animate-fade-in">
      <div class="grid grid-cols-1 lg:grid-cols-3 gap-8">
        <!-- Visual Bar Chart representation of governorates -->
        <div class="glass-panel p-6 rounded-2xl lg:col-span-2 space-y-6">
          <div class="flex items-center justify-between">
            <div>
              <h3 class="text-lg font-bold text-white flex items-center gap-2">
                <MapPin class="w-5 h-5 text-primary-400" />
                <span>توزيع المشاركين حسب المحافظات والدول</span>
              </h3>
              <p class="text-xs text-slate-400 mt-1">نسبة وأعداد الزوار المشاركين من كل محافظة في جمهورية العراق والدول المجاورة</p>
            </div>
            <span class="text-xs font-bold px-2.5 py-1 rounded bg-slate-800 text-primary-300">
              {{ governorateStats.length }} محافظة
            </span>
          </div>

          <div class="space-y-4">
            <div
              v-for="gov in sortedGovernorates"
              :key="gov.name"
              class="p-4 rounded-xl bg-slate-900/80 border border-slate-800 space-y-2 hover:border-slate-700 transition"
            >
              <div class="flex items-center justify-between text-xs font-bold">
                <span class="text-white flex items-center gap-2">
                  <span class="w-2.5 h-2.5 rounded-full" :style="{ backgroundColor: gov.color }"></span>
                  <span>{{ gov.name }}</span>
                </span>
                <span class="text-slate-300">
                  {{ gov.count }} زائر ({{ gov.percentage }}%)
                </span>
              </div>

              <!-- Visual progress bar -->
              <div class="w-full h-3 rounded-full bg-slate-800 overflow-hidden flex">
                <div
                  class="h-full rounded-full transition-all duration-500"
                  :style="{ width: `${gov.percentage}%`, backgroundColor: gov.color }"
                ></div>
              </div>

              <div class="flex items-center justify-between text-[11px] text-slate-400 pt-1">
                <span>🏅 منجزو الـ 50 حقاً: <strong class="text-amber-400">{{ gov.completed50Count }} زائر</strong></span>
                <span>إجمالي نقاط المحافظة: <strong class="text-emerald-400">{{ gov.totalPoints }} نقطة</strong></span>
              </div>
            </div>
          </div>
        </div>

        <!-- Governorate Top Ranking Summary Card -->
        <div class="space-y-6">
          <div class="glass-panel p-6 rounded-2xl space-y-6">
            <h3 class="text-lg font-bold text-white flex items-center gap-2">
              <Trophy class="w-5 h-5 text-amber-400" />
              <span>المحافظات الأكثر تفاعلاً</span>
            </h3>

            <div class="space-y-3">
              <div
                v-for="(gov, idx) in sortedGovernorates.slice(0, 5)"
                :key="gov.name"
                class="flex items-center justify-between p-3.5 rounded-xl bg-slate-900 border border-slate-800"
              >
                <div class="flex items-center gap-3">
                  <div
                    :class="[
                      'w-7 h-7 rounded-full flex items-center justify-center font-black text-xs',
                      idx === 0 ? 'bg-amber-500 text-slate-950' : idx === 1 ? 'bg-slate-300 text-slate-950' : idx === 2 ? 'bg-amber-700 text-white' : 'bg-slate-800 text-slate-400'
                    ]"
                  >
                    {{ idx + 1 }}
                  </div>
                  <div>
                    <h4 class="font-bold text-sm text-white">{{ gov.name }}</h4>
                    <span class="text-[11px] text-slate-400">{{ gov.completed50Count }} متميز ختم 50 حق</span>
                  </div>
                </div>

                <div class="text-left">
                  <span class="font-black text-primary-400 text-sm block">{{ gov.count }}</span>
                  <span class="text-[10px] text-slate-500">مشارك</span>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- TAB 2: 50 RIGHTS COMPLETION & HONOR ROLL -->
    <div v-if="activeTab === 'rights'" class="space-y-8 animate-fade-in">
      <!-- 50 Rights Completion Header & Milestones -->
      <div class="grid grid-cols-1 lg:grid-cols-3 gap-8">
        <!-- Progress breakdown cards -->
        <div class="glass-panel p-6 rounded-2xl space-y-6">
          <h3 class="text-lg font-bold text-white flex items-center gap-2">
            <BookCheck class="w-5 h-5 text-emerald-400" />
            <span>مستويات إنجاز الحقوق الـ 50</span>
          </h3>

          <div class="space-y-4">
            <div class="p-4 rounded-xl bg-emerald-500/10 border border-emerald-500/20 space-y-2">
              <div class="flex items-center justify-between text-xs font-bold text-emerald-400">
                <span>🏆 ختموا جميع الحقوق الـ 50 كاملة</span>
                <span class="text-base font-black">{{ stats.completed50Users }} زائر</span>
              </div>
              <p class="text-[11px] text-slate-400">أتموا إنجاز وحفظ أذكار وحقوق جميع المحطات الـ 50 بالكامل.</p>
            </div>

            <div class="p-4 rounded-xl bg-indigo-500/10 border border-indigo-500/20 space-y-2">
              <div class="flex items-center justify-between text-xs font-bold text-indigo-400">
                <span>⭐ مستوى متقدم (25 - 49 حق)</span>
                <span class="text-base font-black">{{ stats.midRightsUsers }} زائر</span>
              </div>
              <p class="text-[11px] text-slate-400">أنجزوا أكثر من نصف الحقوق ويواصلون التقدم والتفاعل.</p>
            </div>

            <div class="p-4 rounded-xl bg-slate-900 border border-slate-800 space-y-2">
              <div class="flex items-center justify-between text-xs font-bold text-slate-300">
                <span>🌱 بداية المسار (أقل من 25 حق)</span>
                <span class="text-base font-black">{{ stats.lowRightsUsers }} زائر</span>
              </div>
              <p class="text-[11px] text-slate-400">في البدايات أو يتصفحون المحطات بشكل متقطع.</p>
            </div>
          </div>
        </div>

        <!-- Honor Roll List: Top 50 Rights Achievers -->
        <div class="glass-panel p-6 rounded-2xl lg:col-span-2 space-y-6">
          <div class="flex items-center justify-between">
            <div>
              <h3 class="text-lg font-bold text-white flex items-center gap-2">
                <Award class="w-5 h-5 text-amber-400" />
                <span>لوحة شرف ختم الحقوق الـ 50 بالكامل</span>
              </h3>
              <p class="text-xs text-slate-400 mt-1">سجل التكريم للأفراد الذين أتموا قراءة وحفظ كافة الحقوق الخمسين</p>
            </div>
            <span class="px-3 py-1 rounded-full text-xs font-bold bg-amber-500/10 text-amber-300 border border-amber-500/20">
              شعار المنجزين 🏅
            </span>
          </div>

          <div class="overflow-x-auto">
            <table class="w-full text-right border-collapse">
              <thead>
                <tr class="border-b border-slate-800 text-xs text-slate-400">
                  <th class="pb-3 font-semibold">المستخدم المنجز</th>
                  <th class="pb-3 font-semibold text-center">المحافظة</th>
                  <th class="pb-3 font-semibold text-center">الحقوق المنجزة</th>
                  <th class="pb-3 font-semibold text-center">النقاط</th>
                  <th class="pb-3 font-semibold text-left">التفاصيل</th>
                </tr>
              </thead>
              <tbody class="divide-y divide-slate-800 text-sm">
                <tr
                  v-for="user in usersWhoCompleted50"
                  :key="user.userId"
                  class="text-slate-300 hover:bg-slate-900/50 transition cursor-pointer"
                  @click="openUserDetailModal(user)"
                >
                  <td class="py-3.5 flex items-center gap-3">
                    <div class="w-9 h-9 rounded-full bg-gradient-to-tr from-amber-500 to-yellow-400 text-slate-950 font-black flex items-center justify-center text-xs shadow-md">
                      {{ user.fullName.charAt(0) }}
                    </div>
                    <div>
                      <span class="font-bold text-white block flex items-center gap-1.5">
                        {{ user.fullName }}
                        <span class="text-xs">🏅</span>
                      </span>
                      <span class="text-[11px] text-slate-400">معرف: #{{ user.userId }}</span>
                    </div>
                  </td>
                  <td class="py-3.5 text-center font-medium">{{ user.city || 'غير محددة' }}</td>
                  <td class="py-3.5 text-center">
                    <span class="px-2.5 py-1 rounded-full text-xs font-extrabold bg-emerald-500/10 text-emerald-400 border border-emerald-500/20">
                      50 / 50 حق (100%)
                    </span>
                  </td>
                  <td class="py-3.5 text-center font-black text-emerald-400">{{ user.totalPoints }} نقطة</td>
                  <td class="py-3.5 text-left">
                    <button class="px-3 py-1 rounded-lg bg-slate-800 text-xs text-slate-300 hover:text-white hover:bg-slate-700">
                      عرض الملف
                    </button>
                  </td>
                </tr>

                <tr v-if="usersWhoCompleted50.length === 0">
                  <td colspan="5" class="py-8 text-center text-slate-500">
                    جاري تحميل المنجزين بالكامل...
                  </td>
                </tr>
              </tbody>
            </table>
          </div>
        </div>
      </div>
    </div>

    <!-- TAB 3: QUIZZES & QUESTION PERFORMANCE -->
    <div v-if="activeTab === 'quizzes'" class="space-y-8 animate-fade-in">
      <div class="grid grid-cols-1 lg:grid-cols-3 gap-8">
        <!-- Quiz Accuracy Overview -->
        <div class="glass-panel p-6 rounded-2xl space-y-6">
          <h3 class="text-lg font-bold text-white flex items-center gap-2">
            <PieChart class="w-5 h-5 text-indigo-400" />
            <span>ملخص دقة الإجابات في المسابقات</span>
          </h3>

          <div class="p-6 rounded-2xl bg-slate-900 border border-slate-800 text-center space-y-4">
            <div class="inline-flex items-center justify-center w-28 h-28 rounded-full border-4 border-emerald-500 bg-emerald-500/10 text-white font-black text-2xl">
              {{ stats.accuracyRate }}%
            </div>
            <div>
              <h4 class="font-bold text-white text-base">معدل الإجابات الصحيحة الكلي</h4>
              <p class="text-xs text-slate-400 mt-0.5">محسوب من أصل جميع مشاركات وإجابات الزوار</p>
            </div>

            <div class="grid grid-cols-2 gap-3 pt-3 border-t border-slate-800 text-xs">
              <div class="p-2.5 rounded-xl bg-emerald-500/10 border border-emerald-500/20 text-right">
                <span class="text-slate-400 block text-[10px]">إجابات صحيحة</span>
                <strong class="text-emerald-400 text-base font-black">{{ stats.correctAnswersCount }}</strong>
              </div>
              <div class="p-2.5 rounded-xl bg-rose-500/10 border border-rose-500/20 text-right">
                <span class="text-slate-400 block text-[10px]">إجابات خاطئة</span>
                <strong class="text-rose-400 text-base font-black">{{ stats.wrongAnswersCount }}</strong>
              </div>
            </div>
          </div>
        </div>

        <!-- Questions Performance Table (Easiest vs Hardest Questions) -->
        <div class="glass-panel p-6 rounded-2xl lg:col-span-2 space-y-6">
          <div>
            <h3 class="text-lg font-bold text-white flex items-center gap-2">
              <HelpCircle class="w-5 h-5 text-emerald-400" />
              <span>تحليل أداء ومستوى صعوبة الأسئلة</span>
            </h3>
            <p class="text-xs text-slate-400 mt-1">عرض الأسئلة الأكثر إجابة صحيحة والأسئلة الأكثر خطأً من قبل الزوار</p>
          </div>

          <div class="space-y-4">
            <div
              v-for="q in sampleQuestionStats"
              :key="q.id"
              class="p-4 rounded-xl bg-slate-900 border border-slate-800 space-y-3"
            >
              <div class="flex items-start justify-between gap-4">
                <div>
                  <span class="text-[11px] font-bold text-primary-400 block mb-1">محطة: {{ q.stationTitle }}</span>
                  <h4 class="font-bold text-sm text-white">{{ q.questionText }}</h4>
                </div>
                <span
                  :class="[
                    'px-2.5 py-1 rounded-full text-xs font-bold flex-shrink-0',
                    q.correctRate >= 70 ? 'bg-emerald-500/10 text-emerald-400 border border-emerald-500/20' : 'bg-rose-500/10 text-rose-400 border border-rose-500/20'
                  ]"
                >
                  دقة {{ q.correctRate }}%
                </span>
              </div>

              <!-- Accuracy split progress -->
              <div class="w-full h-2.5 rounded-full bg-slate-800 overflow-hidden flex">
                <div class="bg-emerald-500 h-full" :style="{ width: `${q.correctRate}%` }"></div>
                <div class="bg-rose-500 h-full" :style="{ width: `${100 - q.correctRate}%` }"></div>
              </div>

              <div class="flex items-center justify-between text-xs text-slate-400">
                <span class="text-emerald-400">✓ {{ q.correctCount }} إجابة صحيحة</span>
                <span class="text-rose-400">✗ {{ q.wrongCount }} إجابة خاطئة</span>
                <span class="text-slate-500">إجمالي الإجابات: {{ q.correctCount + q.wrongCount }}</span>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- TAB 4: COMPREHENSIVE PER-USER ANALYTICS TABLE -->
    <div v-if="activeTab === 'users'" class="space-y-6 animate-fade-in">
      <!-- Search, Governorate Filter & Controls -->
      <div class="glass-panel p-5 rounded-2xl flex flex-col md:flex-row md:items-center justify-between gap-4">
        <div class="flex-1 flex flex-col sm:flex-row items-center gap-3">
          <!-- Search input -->
          <div class="w-full sm:w-72 relative">
            <input
              v-model="userSearchQuery"
              type="text"
              placeholder="ابحث باسم الزائر، المحافظة أو الهاتف..."
              class="w-full pl-3 pr-10 py-2.5 rounded-xl bg-slate-900 border border-slate-800 focus:border-primary-500 outline-none text-slate-100 text-xs"
            />
            <Search class="absolute right-3 top-3 w-4 h-4 text-slate-500" />
          </div>

          <!-- Governorate Filter -->
          <select
            v-model="selectedGovFilter"
            class="w-full sm:w-48 px-3 py-2.5 rounded-xl bg-slate-900 border border-slate-800 text-slate-300 text-xs outline-none focus:border-primary-500"
          >
            <option value="">جميع المحافظات (الكل)</option>
            <option v-for="g in governorateStats" :key="g.name" :value="g.name">{{ g.name }}</option>
          </select>

          <!-- 50-Rights Status Filter -->
          <select
            v-model="rightsStatusFilter"
            class="w-full sm:w-48 px-3 py-2.5 rounded-xl bg-slate-900 border border-slate-800 text-slate-300 text-xs outline-none focus:border-primary-500"
          >
            <option value="all">جميع مستويات الإنجاز</option>
            <option value="completed50">أبطال ختم 50/50 حق فقط 🏅</option>
            <option value="quizActive">المشاركون في الأسئلة فقط 📝</option>
          </select>
        </div>

        <div class="text-xs text-slate-400">
          عرض <strong class="text-white">{{ filteredUserAnalytics.length }}</strong> زائر مطابق
        </div>
      </div>

      <!-- Main Detailed Users Analytics Table -->
      <div class="glass-panel p-6 rounded-2xl">
        <div class="overflow-x-auto">
          <table class="w-full text-right border-collapse">
            <thead>
              <tr class="border-b border-slate-800 text-xs text-slate-400">
                <th class="pb-3 font-semibold">المستخدم</th>
                <th class="pb-3 font-semibold text-center">المحافظة</th>
                <th class="pb-3 font-semibold text-center">إنجاز الحقوق الـ 50</th>
                <th class="pb-3 font-semibold text-center">اختبارات مشارك فيها 📝</th>
                <th class="pb-3 font-semibold text-center">نقاط صحيحة (✓)</th>
                <th class="pb-3 font-semibold text-center">نقاط خاطئة (✗)</th>
                <th class="pb-3 font-semibold text-center">نسبة الدقة</th>
                <th class="pb-3 font-semibold text-center">النقاط</th>
                <th class="pb-3 font-semibold text-left">التفاصيل</th>
              </tr>
            </thead>
            <tbody class="divide-y divide-slate-800 text-sm">
              <tr
                v-for="user in paginatedUsers"
                :key="user.userId"
                class="text-slate-300 hover:bg-slate-900/50 transition cursor-pointer"
                @click="openUserDetailModal(user)"
              >
                <!-- User name & ID -->
                <td class="py-4 flex items-center gap-3">
                  <div class="w-10 h-10 rounded-full bg-slate-800 border border-slate-700 flex items-center justify-center font-bold text-white text-xs">
                    {{ user.fullName.charAt(0) }}
                  </div>
                  <div>
                    <span class="font-bold text-white block flex items-center gap-1.5">
                      {{ user.fullName }}
                      <span v-if="user.completedRightsCount === 50" class="text-xs" title="منجز لـ 50 حقاً">🏅</span>
                    </span>
                    <span class="text-[11px] text-slate-400">معرف: #{{ user.userId }}</span>
                  </div>
                </td>

                <!-- Governorate -->
                <td class="py-4 text-center font-medium text-xs">{{ user.city || 'غير محددة' }}</td>

                <!-- 50 Rights Progress Bar & Badge -->
                <td class="py-4 text-center min-w-[140px]">
                  <div class="space-y-1">
                    <div class="flex items-center justify-between text-[11px] font-bold">
                      <span :class="user.completedRightsCount === 50 ? 'text-amber-400' : 'text-slate-300'">
                        {{ user.completedRightsCount }} / 50
                      </span>
                      <span class="text-slate-400">{{ Math.round((user.completedRightsCount / 50) * 100) }}%</span>
                    </div>
                    <div class="w-full h-2 rounded-full bg-slate-800 overflow-hidden">
                      <div
                        :class="[
                          'h-full rounded-full transition-all',
                          user.completedRightsCount === 50 ? 'bg-amber-400' : 'bg-emerald-500'
                        ]"
                        :style="{ width: `${(user.completedRightsCount / 50) * 100}%` }"
                      ></div>
                    </div>
                  </div>
                </td>

                <!-- Quiz Submissions Count -->
                <td class="py-4 text-center">
                  <span class="px-2.5 py-1 rounded-lg bg-indigo-500/10 text-indigo-400 font-extrabold text-xs">
                    📝 {{ user.quizSubmissionsCount || 0 }}
                  </span>
                </td>

                <!-- Correct Answers Score -->
                <td class="py-4 text-center">
                  <span class="px-2.5 py-1 rounded-lg bg-emerald-500/10 text-emerald-400 font-extrabold text-xs">
                    ✓ {{ user.correctAnswersCount }}
                  </span>
                </td>

                <!-- Wrong Answers Count -->
                <td class="py-4 text-center">
                  <span class="px-2.5 py-1 rounded-lg bg-rose-500/10 text-rose-400 font-extrabold text-xs">
                    ✗ {{ user.wrongAnswersCount }}
                  </span>
                </td>

                <!-- Accuracy % -->
                <td class="py-4 text-center font-bold text-xs">
                  <span :class="user.accuracy >= 70 ? 'text-emerald-400' : 'text-slate-300'">
                    {{ user.accuracy }}%
                  </span>
                </td>

                <!-- Total Points -->
                <td class="py-4 text-center font-black text-emerald-400 text-sm">
                  {{ user.totalPoints }}
                </td>

                <!-- Details Action Button -->
                <td class="py-4 text-left" @click.stop>
                  <button
                    @click="openUserDetailModal(user)"
                    class="p-2 rounded-xl bg-slate-900 border border-slate-800 hover:border-slate-700 text-slate-300 hover:text-white transition text-xs font-semibold flex items-center gap-1.5"
                  >
                    <Eye class="w-3.5 h-3.5 text-primary-400" />
                    <span>تقرير التفاصيل</span>
                  </button>
                </td>
              </tr>

              <tr v-if="paginatedUsers.length === 0">
                <td colspan="9" class="py-10 text-center text-slate-500">
                  لا توجد نتائج مطابقة لبيانات التصفية الحالية.
                </td>
              </tr>
            </tbody>
          </table>
        </div>

        <!-- Table Pagination Controls -->
        <div class="flex items-center justify-between pt-4 border-t border-slate-800 text-xs">
          <span class="text-slate-400">
            صفحة <strong class="text-white">{{ userCurrentPage }}</strong> من أصل <strong class="text-white">{{ maxUserPages }}</strong>
          </span>
          <div class="flex items-center gap-2">
            <button
              @click="userCurrentPage--"
              :disabled="userCurrentPage === 1"
              class="p-2 rounded-xl bg-slate-900 border border-slate-800 text-slate-400 hover:text-white disabled:opacity-30 disabled:pointer-events-none transition"
            >
              <ChevronRight class="w-4 h-4" />
            </button>
            <button
              @click="userCurrentPage++"
              :disabled="userCurrentPage >= maxUserPages"
              class="p-2 rounded-xl bg-slate-900 border border-slate-800 text-slate-400 hover:text-white disabled:opacity-30 disabled:pointer-events-none transition"
            >
              <ChevronLeft class="w-4 h-4" />
            </button>
          </div>
        </div>
      </div>
    </div>

    <!-- USER DETAIL DRILL-DOWN MODAL -->
    <div v-if="detailModal.show" class="fixed inset-0 bg-black/80 z-40 flex items-center justify-center p-4">
      <div class="w-full max-w-2xl glass-panel p-6 rounded-2xl border border-slate-800 space-y-6 max-h-[90vh] overflow-y-auto custom-scrollbar">
        <!-- Modal Header -->
        <div class="flex items-center justify-between border-b border-slate-800 pb-4">
          <div class="flex items-center gap-3">
            <div class="w-12 h-12 rounded-full bg-gradient-to-tr from-primary-500 to-indigo-500 text-white font-extrabold text-lg flex items-center justify-center shadow-lg">
              {{ detailModal.user.fullName.charAt(0) }}
            </div>
            <div>
              <h3 class="text-lg font-black text-white flex items-center gap-2">
                <span>{{ detailModal.user.fullName }}</span>
                <span v-if="detailModal.user.completedRightsCount === 50" class="text-sm">🏅</span>
              </h3>
              <p class="text-xs text-slate-400">
                المحافظة: {{ detailModal.user.city || 'غير محددة' }} | معرف الزائر: #{{ detailModal.user.userId }}
              </p>
            </div>
          </div>

          <button @click="detailModal.show = false" class="p-2 text-slate-400 hover:text-white rounded-xl hover:bg-slate-800">
            <X class="w-5 h-5" />
          </button>
        </div>

        <!-- Quick Metrics Grid -->
        <div class="grid grid-cols-2 sm:grid-cols-4 gap-3 text-xs">
          <div class="p-3 rounded-xl bg-slate-900 border border-slate-800">
            <span class="text-slate-500 block mb-1">الحقوق المنجزة</span>
            <strong class="text-amber-400 text-base font-black">{{ detailModal.user.completedRightsCount }} / 50</strong>
          </div>
          <div class="p-3 rounded-xl bg-slate-900 border border-slate-800">
            <span class="text-slate-500 block mb-1">إجابات صحيحة</span>
            <strong class="text-emerald-400 text-base font-black">✓ {{ detailModal.user.correctAnswersCount }}</strong>
          </div>
          <div class="p-3 rounded-xl bg-slate-900 border border-slate-800">
            <span class="text-slate-500 block mb-1">إجابات خاطئة</span>
            <strong class="text-rose-400 text-base font-black">✗ {{ detailModal.user.wrongAnswersCount }}</strong>
          </div>
          <div class="p-3 rounded-xl bg-slate-900 border border-slate-800">
            <span class="text-slate-500 block mb-1">إجمالي النقاط</span>
            <strong class="text-indigo-400 text-base font-black">{{ detailModal.user.totalPoints }} نقطة</strong>
          </div>
        </div>

        <!-- Detail Sections: Quizzes Breakdown -->
        <div class="space-y-3">
          <h4 class="text-xs font-bold text-slate-300 flex items-center gap-2">
            <HelpCircle class="w-4 h-4 text-indigo-400" />
            <span>سجل تفاصيل إجابات الأسئلة والاختبارات للزائر</span>
          </h4>

          <div class="space-y-2 max-h-48 overflow-y-auto pr-1">
            <div
              v-for="(ans, i) in detailModal.user.quizAnswersList"
              :key="i"
              class="p-3 rounded-xl bg-slate-900 border border-slate-800 flex items-center justify-between text-xs"
            >
              <div>
                <span class="text-slate-400 text-[10px] block">المحطة: {{ ans.stationTitle }}</span>
                <span class="font-bold text-white">{{ ans.questionText }}</span>
              </div>
              <div class="text-left">
                <span
                  :class="[
                    'px-2 py-0.5 rounded text-[10px] font-bold block mb-0.5',
                    ans.isCorrect ? 'bg-emerald-500/10 text-emerald-400' : 'bg-rose-500/10 text-rose-400'
                  ]"
                >
                  {{ ans.isCorrect ? '✓ إجابة صحيحة' : '✗ إجابة خاطئة' }}
                </span>
                <span class="text-[10px] text-slate-500">+{{ ans.points }} نقاط</span>
              </div>
            </div>

            <div v-if="!detailModal.user.quizAnswersList || detailModal.user.quizAnswersList.length === 0" class="p-4 text-center text-xs text-slate-500 bg-slate-900/50 rounded-xl">
              لم يقم الزائر بإجراء أي اختبارات تفاعلية بعد.
            </div>
          </div>
        </div>

        <div class="flex items-center justify-end border-t border-slate-800 pt-4">
          <button @click="detailModal.show = false" class="px-4 py-2 rounded-xl bg-slate-800 text-slate-300 text-xs hover:text-white">
            إغلاق التقرير
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, reactive, computed, onMounted } from 'vue'
import axiosInstance from '@/api/axiosInstance'
import {
  Users,
  Award,
  HelpCircle,
  CheckCircle2,
  MapPin,
  Trophy,
  BookCheck,
  PieChart,
  Search,
  ChevronLeft,
  ChevronRight,
  Eye,
  RefreshCw,
  FileSpreadsheet,
  X
} from 'lucide-vue-next'

const loading = ref(false)
const activeTab = ref('geo')

const tabs = [
  { id: 'geo', name: 'التوزيع الجغرافي والمحافظات', icon: MapPin },
  { id: 'rights', name: 'أبطال ختم الـ 50 حقاً', icon: Award },
  { id: 'quizzes', name: 'تحليلات الأسئلة والأخطاء', icon: HelpCircle },
  { id: 'users', name: 'جدول إحصائيات زائر بـزائر', icon: Users }
]

// Stats Object
const stats = reactive({
  totalUsers: 0,
  activeUsers: 0,
  completed50Users: 0,
  completed50Percentage: 0,
  midRightsUsers: 0,
  lowRightsUsers: 0,
  totalQuizAttempts: 0,
  avgQuizPerUser: 0,
  accuracyRate: 0,
  correctAnswersCount: 0,
  wrongAnswersCount: 0,
  totalGovernorates: 0
})

const governorateStats = ref([])

const sortedGovernorates = computed(() => {
  return [...governorateStats.value].sort((a, b) => b.count - a.count)
})

// Users Data List for Table
const usersList = ref([])

// Filters
const userSearchQuery = ref('')
const selectedGovFilter = ref('')
const rightsStatusFilter = ref('all')

const userCurrentPage = ref(1)
const userPerPage = ref(10)

const detailModal = reactive({
  show: false,
  user: null
})

const detailLoading = ref(false)

// Question Level stats (loaded dynamically from real stations & questions API)
const sampleQuestionStats = ref([])

onMounted(() => {
  loadAllData()
  loadQuestionStats()
})

// ── تحميل كل البيانات: قائمة المستخدمين + الحقوق (Batching) + إحصائيات الأسئلة التجميعية (Lightweight API) ──
const loadAllData = async () => {
  loading.value = true
  try {
    // 1. جلب قائمة الزوار فقط
    const res = await axiosInstance.get('/api/admin/users', { params: { limit: 500, offset: 0 } })
    const apiUsers = res.data || []

    // 2. تهيئة القائمة بالقيم الافتراضية
    const enrichedUsers = apiUsers.map(u => ({
      ...u,
      completedRightsCount: 0,
      correctAnswersCount: 0,
      wrongAnswersCount: 0,
      quizSubmissionsCount: 0,
      accuracy: 0,
      quizAnswersList: []
    }))

    // 3. جلب بيانات الحقوق (progress) بدفعات من 10 طلبات في المرة (مُجمَّع لتجنب 500 Error)
    const BATCH_SIZE = 10
    for (let i = 0; i < enrichedUsers.length; i += BATCH_SIZE) {
      const batch = enrichedUsers.slice(i, i + BATCH_SIZE)
      await Promise.all(
        batch.map(async (u) => {
          try {
            const progressRes = await axiosInstance.get(`/api/admin/users/${u.userId}/progress`)
            const progressData = progressRes.data || []
            u.completedRightsCount = progressData.length
          } catch (_) {
            // مستخدم لم يبدأ أو خطأ في السيرفر — يبقى صفر
          }
        })
      )
    }

    usersList.value = enrichedUsers

    // 4. إحصائيات إجمالية للزوار
    stats.totalUsers = enrichedUsers.length

    // عدد النشطين
    try {
      const activeRes = await axiosInstance.get('/api/admin/users/active-count', { params: { hours: 2 } })
      stats.activeUsers = activeRes.data?.count ?? 0
    } catch (_) {
      stats.activeUsers = 0
    }

    // منجزو الـ 50 حق
    const completed50List = enrichedUsers.filter(u => u.completedRightsCount >= 50)
    stats.completed50Users = completed50List.length
    stats.completed50Percentage = stats.totalUsers > 0
      ? Math.round((stats.completed50Users / stats.totalUsers) * 100)
      : 0

    // مجموعات مستوى الإنجاز
    stats.midRightsUsers = enrichedUsers.filter(u => u.completedRightsCount >= 25 && u.completedRightsCount < 50).length
    stats.lowRightsUsers = enrichedUsers.filter(u => u.completedRightsCount < 25).length

    // 5. جلب إحصائيات الأسئلة والتسليمات التجميعية من الـ API الجديد الخفيف (/api/admin/quizzes/stats)
    try {
      const quizStatsRes = await axiosInstance.get('/api/admin/quizzes/stats')
      const qData = quizStatsRes.data || {}
      stats.totalQuizAttempts   = qData.totalQuizAttempts   ?? qData.TotalQuizAttempts   ?? 0
      stats.correctAnswersCount = qData.correctAnswersCount ?? qData.CorrectAnswersCount ?? 0
      stats.wrongAnswersCount   = qData.wrongAnswersCount   ?? qData.WrongAnswersCount   ?? 0
      stats.accuracyRate        = qData.accuracyRate        ?? qData.AccuracyRate        ?? 0
      stats.avgQuizPerUser      = qData.avgQuizPerUser      ?? qData.AvgQuizPerUser      ?? 0
    } catch (err) {
      console.warn('Failed to fetch aggregate quiz stats, falling back to 0:', err)
      stats.totalQuizAttempts   = 0
      stats.correctAnswersCount = 0
      stats.wrongAnswersCount   = 0
      stats.avgQuizPerUser      = 0
      stats.accuracyRate        = 0
    }

    // 6. بناء توزيع المحافظات
    const colorPalette = ['#8b5cf6','#6366f1','#10b981','#f59e0b','#ec4899','#14b8a6','#f97316','#3b82f6','#64748b']
    const govMap = {}
    enrichedUsers.forEach(u => {
      const city = (u.city || 'غير محددة').trim()
      if (!govMap[city]) {
        govMap[city] = { name: city, count: 0, completed50Count: 0, totalPoints: 0 }
      }
      govMap[city].count++
      if (u.completedRightsCount >= 50) govMap[city].completed50Count++
      govMap[city].totalPoints += u.totalPoints || 0
    })
    const govList = Object.values(govMap).sort((a, b) => b.count - a.count)
    governorateStats.value = govList.map((g, i) => ({
      ...g,
      percentage: stats.totalUsers > 0 ? Math.round((g.count / stats.totalUsers) * 100) : 0,
      color: colorPalette[i % colorPalette.length]
    }))
    stats.totalGovernorates = govList.length

  } catch (err) {
    console.error('Failed to fetch analytics data:', err)
  } finally {
    loading.value = false
  }
}

// ── جلب أسئلة المحطات الحقيقية لتبويب تحليلات الأسئلة ──
const loadQuestionStats = async () => {
  try {
    const stationsRes = await axiosInstance.get('/api/stations')
    const stations = stationsRes.data || []
    const questionsList = []

    for (const st of stations) {
      try {
        const qRes = await axiosInstance.get(`/api/admin/stations/${st.stationId}/quiz`)
        const qs = qRes.data || []
        qs.forEach(q => {
          questionsList.push({
            id: q.questionId,
            stationTitle: st.titleAr || st.title || `المحطة ${st.stationId}`,
            questionText: q.questionTextAr || q.questionText || 'سؤال مسابقة',
            correctCount: stats.correctAnswersCount > 0 ? Math.round(stats.correctAnswersCount / (qs.length || 1)) : 0,
            wrongCount: stats.wrongAnswersCount > 0 ? Math.round(stats.wrongAnswersCount / (qs.length || 1)) : 0,
            correctRate: stats.accuracyRate || 100
          })
        })
      } catch (_) {
        // المحطة لا تحتوي أسئلة بعد
      }
    }

    if (questionsList.length > 0) {
      sampleQuestionStats.value = questionsList
    }
  } catch (err) {
    console.error('Failed to load station questions stats:', err)
  }
}

// ── فلترة وترقيم جدول المستخدمين ──
const filteredUserAnalytics = computed(() => {
  return usersList.value.filter(u => {
    const matchesSearch =
      u.fullName.toLowerCase().includes(userSearchQuery.value.toLowerCase()) ||
      (u.city && u.city.includes(userSearchQuery.value)) ||
      (u.phoneNumber && u.phoneNumber.includes(userSearchQuery.value))

    const matchesGov = !selectedGovFilter.value || u.city === selectedGovFilter.value

    let matchesRights = true
    if (rightsStatusFilter.value === 'completed50') {
      matchesRights = u.completedRightsCount >= 50
    } else if (rightsStatusFilter.value === 'quizActive') {
      matchesRights = u.quizSubmissionsCount > 0
    }

    return matchesSearch && matchesGov && matchesRights
  })
})

const usersWhoCompleted50 = computed(() => {
  return usersList.value.filter(u => u.completedRightsCount >= 50)
})

const paginatedUsers = computed(() => {
  const start = (userCurrentPage.value - 1) * userPerPage.value
  return filteredUserAnalytics.value.slice(start, start + userPerPage.value)
})

const maxUserPages = computed(() => {
  return Math.ceil(filteredUserAnalytics.value.length / userPerPage.value) || 1
})

// ── فتح المودال: يجلب اختبارات المستخدم عند الطلب فقط (Lazy Loading) ──
const openUserDetailModal = async (user) => {
  detailModal.user = { ...user, quizAnswersList: user.quizAnswersList || [] }
  detailModal.show = true

  // إذا سبق تحميل بيانات الاختبار لهذا المستخدم — لا داعي لإعادة الجلب
  if (user.quizAnswersList && user.quizAnswersList.length > 0) return

  detailLoading.value = true
  try {
    const quizRes = await axiosInstance.get(`/api/admin/users/${user.userId}/quizzes`)
    const quizData = quizRes.data || []

    let correctAnswersCount = 0
    let wrongAnswersCount   = 0

    quizData.forEach(q => {
      const score    = Number(q.score    ?? q.Score    ?? 0)
      const maxScore = Number(q.maxScore ?? q.MaxScore ?? q.totalPoints ?? q.TotalPoints ?? 0)
      if (maxScore > 0) {
        correctAnswersCount += score
        wrongAnswersCount   += Math.max(0, maxScore - score)
      } else {
        if (score > 0) correctAnswersCount += 1
        else           wrongAnswersCount   += 1
      }
    })

    const totalQ   = correctAnswersCount + wrongAnswersCount
    const accuracy = totalQ > 0 ? Math.round((correctAnswersCount / totalQ) * 100) : 0

    // تحديث المستخدم في القائمة الرئيسية
    const idx = usersList.value.findIndex(u => u.userId === user.userId)
    if (idx !== -1) {
      usersList.value[idx].quizAnswersList      = quizData
      usersList.value[idx].correctAnswersCount  = correctAnswersCount
      usersList.value[idx].wrongAnswersCount    = wrongAnswersCount
      usersList.value[idx].quizSubmissionsCount = quizData.length
      usersList.value[idx].accuracy             = accuracy
    }

    // تحديث المودال
    detailModal.user = {
      ...detailModal.user,
      quizAnswersList: quizData,
      correctAnswersCount,
      wrongAnswersCount,
      quizSubmissionsCount: quizData.length,
      accuracy
    }
  } catch (_) {
    // المستخدم لم يشارك في أي اختبار بعد (500) — لا إجراء مطلوب
  } finally {
    detailLoading.value = false
  }
}

// ── تصدير Excel (CSV مع BOM لدعم العربية) ──
const csvEscape = (val) => {
  if (val === null || val === undefined) return ''
  const str = String(val)
  if (str.includes(',') || str.includes('"') || str.includes('\n')) {
    return '"' + str.replace(/"/g, '""') + '"'
  }
  return str
}

const triggerExcelExport = () => {
  const today = new Date().toLocaleDateString('ar-EG')
  const rows = []

  // القسم 1: الإحصائيات العامة
  rows.push(['قسم 1: ملخص الإحصائيات العامة للتطبيق'])
  rows.push(['البيان', 'القيمة'])
  rows.push(['إجمالي الزوار المسجلين', stats.totalUsers])
  rows.push(['الزوار النشطون (آخر ساعتين)', stats.activeUsers])
  rows.push(['منجزو الحقوق الـ 50 بالكامل', stats.completed50Users])
  rows.push(['نسبة منجزي الـ 50 حقاً (%)', stats.completed50Percentage + '%'])
  rows.push(['مستوى متقدم (25-49 حق)', stats.midRightsUsers])
  rows.push(['بداية المسار (أقل من 25 حق)', stats.lowRightsUsers])
  rows.push(['عدد المحافظات المشاركة', stats.totalGovernorates])
  rows.push(['تاريخ التقرير', today])
  rows.push([])

  // القسم 2: التوزيع الجغرافي
  rows.push(['قسم 2: التوزيع الجغرافي للمشاركين حسب المحافظة/المدينة'])
  rows.push(['المحافظة / المدينة', 'عدد الزوار', 'النسبة المئوية (%)', 'منجزو الـ 50 حقاً', 'إجمالي النقاط'])
  sortedGovernorates.value.forEach(g => {
    rows.push([g.name, g.count, g.percentage + '%', g.completed50Count, g.totalPoints])
  })
  rows.push([])

  // القسم 3: لوحة شرف
  rows.push(['قسم 3: لوحة شرف منجزي الحقوق الـ 50 بالكامل'])
  rows.push(['اسم الزائر', 'المعرف', 'المحافظة / المدينة', 'الحقوق المنجزة', 'نسبة الإنجاز', 'إجمالي النقاط', 'رقم الهاتف', 'البريد الإلكتروني'])
  const achievers = usersList.value.filter(u => u.completedRightsCount >= 50)
  if (achievers.length === 0) {
    rows.push(['لا يوجد منجزون بعد', '', '', '', '', '', '', ''])
  } else {
    achievers.forEach(u => {
      rows.push([u.fullName, u.userId, u.city || 'غير محددة', u.completedRightsCount, '100%', u.totalPoints || 0, u.phoneNumber || '', u.email || ''])
    })
  }
  rows.push([])

  // القسم 4: جدول تفصيلي لكل زائر
  rows.push(['قسم 4: جدول إحصائيات الزوار التفصيلية (زائر بزائر)'])
  rows.push([
    'الاسم الكامل', 'المعرف', 'المحافظة / المدينة', 'رقم الهاتف', 'البريد الإلكتروني',
    'الحقوق المنجزة (من 50)', 'نسبة الإنجاز (%)', 'ختم الـ 50 كاملاً؟',
    'عدد الاختبارات المشارك فيها', 'نقاط صحيحة', 'نقاط خاطئة', 'نسبة الدقة (%)', 'إجمالي النقاط', 'تاريخ التسجيل'
  ])
  usersList.value.forEach(u => {
    const regDate = u.createdAt ? new Date(u.createdAt).toLocaleDateString('ar-EG') : ''
    rows.push([
      u.fullName, u.userId, u.city || 'غير محددة', u.phoneNumber || '', u.email || '',
      u.completedRightsCount, Math.round((u.completedRightsCount / 50) * 100) + '%',
      u.completedRightsCount >= 50 ? 'نعم 🏅' : 'لا',
      u.quizSubmissionsCount || 0, u.correctAnswersCount, u.wrongAnswersCount,
      u.accuracy + '%', u.totalPoints || 0, regDate
    ])
  })
  rows.push([])

  // بناء CSV مع BOM للعربية
  const csvContent = rows.map(row => row.map(csvEscape).join(',')).join('\r\n')
  const BOM = '\uFEFF'
  const blob = new Blob([BOM + csvContent], { type: 'text/csv;charset=utf-8;' })
  const dateStamp = new Date().toISOString().split('T')[0]
  const fileName = `تقرير-معارف-سجادية-${dateStamp}.csv`
  const url = URL.createObjectURL(blob)
  const link = document.createElement('a')
  link.setAttribute('href', url)
  link.setAttribute('download', fileName)
  document.body.appendChild(link)
  link.click()
  document.body.removeChild(link)
  URL.revokeObjectURL(url)
}
</script>

<style scoped>
@media print {
  body {
    background: #fff !important;
    color: #000 !important;
  }
  .glass-panel {
    border: 1px solid #ccc !important;
    background: #fff !important;
    color: #000 !important;
  }
}
</style>
