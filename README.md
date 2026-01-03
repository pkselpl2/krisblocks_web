# 크리스블록 사용방법
/***********************
 * 크리스블록 0.2
 * 진짜 iframe 풀버전
 ***********************/

/* 색상 */
const KRIS_COLOR = {
  default: '#7CDB9C',
  darken: '#5FBF84',
};

/* iframe 생성 */
function getKrisIframe() {
  let iframe = document.getElementById('kris_iframe');
  if (!iframe) {
    iframe = document.createElement('iframe');
    iframe.id = 'kris_iframe';
    iframe.style.position = 'fixed';
    iframe.style.left = '0';
    iframe.style.top = '0';
    iframe.style.width = '100%';
    iframe.style.height = '100%';
    iframe.style.border = '0';
    iframe.style.zIndex = '9999';
    iframe.style.display = 'none';
    iframe.allow = 'fullscreen';
    document.body.appendChild(iframe);
  }
  return iframe;
}

/* 블록 정의 */
const krisBlocks = [

  /* iframe 보이기 */
  {
    name: 'kris_iframe_show',
    template: 'iframe %1 보이기',
    skeleton: 'basic',
    color: KRIS_COLOR.default,
    outerLine: KRIS_COLOR.darken,
    params: [{ type: 'Block', accept: 'string' }],
    def: [{ type: 'text', params: ['https://playentry.org'] }],
    paramsKeyMap: { URL: 0 },
    class: 'text',
    func: (sprite, script) => {
      const iframe = getKrisIframe();
      iframe.src = script.getValue('URL', script);
      iframe.style.display = 'block';
      return;
    },
  },

  /* iframe 숨기기 */
  {
    name: 'kris_iframe_hide',
    template: 'iframe 숨기기',
    skeleton: 'basic',
    color: KRIS_COLOR.default,
    outerLine: KRIS_COLOR.darken,
    params: [],
    def: [],
    paramsKeyMap: {},
    class: 'text',
    func: () => {
      const iframe = document.getElementById('kris_iframe');
      if (iframe) iframe.style.display = 'none';
      return;
    },
  },

  /* iframe 투명도 */
  {
    name: 'kris_iframe_opacity',
    template: 'iframe 투명도 %1 %',
    skeleton: 'basic',
    color: KRIS_COLOR.default,
    outerLine: KRIS_COLOR.darken,
    params: [{ type: 'Block', accept: 'number' }],
    def: [{ type: 'number', params: [100] }],
    paramsKeyMap: { OP: 0 },
    class: 'text',
    func: (sprite, script) => {
      getKrisIframe().style.opacity =
        script.getValue('OP', script) / 100;
      return;
    },
  },

  /* iframe 클릭 차단 */
  {
    name: 'kris_iframe_block_click',
    template: 'iframe 클릭 차단',
    skeleton: 'basic',
    color: KRIS_COLOR.default,
    outerLine: KRIS_COLOR.darken,
    params: [],
    def: [],
    paramsKeyMap: {},
    class: 'text',
    func: () => {
      getKrisIframe().style.pointerEvents = 'none';
      return;
    },
  },

  /* iframe 클릭 통과 */
  {
    name: 'kris_iframe_allow_click',
    template: 'iframe 클릭 통과',
    skeleton: 'basic',
    color: KRIS_COLOR.default,
    outerLine: KRIS_COLOR.darken,
    params: [],
    def: [],
    paramsKeyMap: {},
    class: 'text',
    func: () => {
      getKrisIframe().style.pointerEvents = 'auto';
      return;
    },
  },

  /* (엔트리) 웹사이트 열기 */
  {
    name: 'kris_open_website',
    template: '(엔트리) 웹사이트 %1 열기',
    skeleton: 'basic',
    color: KRIS_COLOR.default,
    outerLine: KRIS_COLOR.darken,
    params: [{ type: 'Block', accept: 'string' }],
    def: [{ type: 'text', params: ['https://playentry.org'] }],
    paramsKeyMap: { URL: 0 },
    class: 'text',
    func: (sprite, script) => {
      window.open(script.getValue('URL', script), '_blank');
      return;
    },
  },

  /* 작품 정지 */
  {
    name: 'kris_project_stop',
    template: '작품 정지하기',
    skeleton: 'basic',
    color: KRIS_COLOR.default,
    outerLine: KRIS_COLOR.darken,
    params: [],
    def: [],
    paramsKeyMap: {},
    class: 'text',
    func: () => {
      if (Entry.engine) Entry.engine.toggleStop();
      return;
    },
  },

  /* 작품 시작 */
  {
    name: 'kris_project_start',
    template: '작품 시작하기',
    skeleton: 'basic',
    color: KRIS_COLOR.default,
    outerLine: KRIS_COLOR.darken,
    params: [],
    def: [],
    paramsKeyMap: {},
    class: 'text',
    func: () => {
      if (Entry.engine) Entry.engine.toggleRun();
      return;
    },
  },

  /* 엔트리 alert */
  {
    name: 'kris_alert',
    template: '(엔트리) %1 알림',
    skeleton: 'basic',
    color: KRIS_COLOR.default,
    outerLine: KRIS_COLOR.darken,
    params: [{ type: 'Block', accept: 'string' }],
    def: [{ type: 'text', params: ['안녕하세요'] }],
    paramsKeyMap: { MSG: 0 },
    class: 'text',
    func: (sprite, script) => {
      alert(script.getValue('MSG', script));
      return;
    },
  },

  /* 크레딧 */
  {
    name: 'kris_credit',
    template: '%1',
    color: EntryStatic.colorSet.common.TRANSPARENT,
    skeleton: 'basic_text',
    params: [{
      type: 'Text',
      text: '이 블록은 GPT와 크리스가 만들었습니다',
      color: EntryStatic.colorSet.common.TEXT,
      align: 'center',
    }],
    def: [],
    paramsKeyMap: {},
    class: 'text',
  },
];

/* LibraryCreator */
window.LibraryCreator = {
  start(blocks, category, name, icon) {
    if (!window.Entry || !Entry.block) {
      new MutationObserver((_, observer) => {
        if (window.Entry && Entry.block) {
          this.start(blocks, category, name, icon);
          observer.disconnect();
        }
      }).observe(document, { subtree: true, childList: true });

      return;
    }

    if (EntryStatic.getAllBlocks().some(block => category == block.category)) return;
    Lang.Blocks[category.toUpperCase()] = name;

    EntryStatic.getAllBlocks = (getAllBlocks => () => [
      ...getAllBlocks(),
      {
        category,
        blocks: blocks.map(v => v.name),
      },
    ])(EntryStatic.getAllBlocks);

    Entry.playground?.blockMenu?._categoryData.push({
      category,
      blocks: [],
    });

    Entry.playground?.blockMenu?._generateCategoryView(Entry.playground.blockMenu._categoryData);
    Entry.playground?.blockMenu?._generateCategoryCode(category);

    Entry.moduleManager?.loadBlocks({
      categoryName: category,
      blockSchemas: blocks.map(block => ({
        blockName: block.name,
        block: Object.assign(block, {
          def: {
            params: block.def,
            type: block.name,
          },
        }),
        isBlockShowBlockMenu: true,
      })),
    });

    Entry.playground?.blockMenu?.setMenu();

    $('head').append(`
      <style>
      #entryCategory${category}{
        background-image:url(${icon});
        background-repeat:no-repeat;
        background-size:20px;
        background-position-y:8px;
      }
      </style>
    `);

    console.log(`[KrisBlock] ${name} 로드 완료`);
  },
};

/* 실행 */
LibraryCreator.start(
  krisBlocks,
  'KRIS',
  '크리스블록',
  'https://raw.githubusercontent.com/pkselpl2/krisblocks/ef37224a6ad7032098f43d0350e0a5d4f2dea9fe/krislogo.svg.svg'
);

console.log('✅ 크리스블록 0.2 적용 완료');
