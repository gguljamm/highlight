<template>
  <div class="relative bg-white p-8">
    <input class="border" v-model="searchText">
    <div v-for="x in arr" :key="searchText" v-html="replaceText(x)"></div>
  </div>
</template>

<script setup>
const arr = [
  'xxx CTF-111 yyy',
  'a CTF-111 bbbba CTF-111 bbb',
  'CTF-111 yyya CTF-111 zzz',
  'xxx 11 yy xx',
]

const searchText = ref('');
const replaceText = (text) => {
  let bodyText = text;
  if (Array.isArray(bodyText)) {
    bodyText = bodyText.join(',');
  } else if (typeof bodyText === 'number') {
    bodyText = `${bodyText}`;
  }
  const reg = bodyText.match(/([A-Z]{3,}-\d+)/);
  if (reg) {
    bodyText = bodyText.replaceAll(reg[0], `<a class="underline text-orange-500" href="https://jira.abouthere.kr/secure/RapidBoard.jspa?rapidView=558&projectKey=CTF&view=detail&selectedIssue=${reg[0]}" target="_blank">${reg[0]}</a>`);
  }
  let newText = '';
  const t = searchText.value.trim();
  // newText = newText.replaceAll(t, `<span class="bg-emerald-200 rounded bg-opacity-50">${ t }</span>`);
  const ele = document.createElement('div');
  ele.innerHTML = bodyText;
  const wrapText = ele.innerText;
  if (t && wrapText.indexOf(t) >= 0) {
    let textIndex = 0;
    let replaceStartIndex = wrapText.indexOf(t);
    let replaceEndIndex = replaceStartIndex >= 0 ? replaceStartIndex + t.length : -1;
    for (let x = 0; x < ele.childNodes.length; x += 1) {
      const innerText = ele.childNodes[x].textContent;
      let tt = '';
      if (innerText.indexOf(t) >= 0) {
        tt = innerText.replaceAll(t, `<span class="bg-emerald-200 rounded bg-opacity-50">${ t }</span>`);
      } else if (replaceEndIndex >= textIndex && replaceStartIndex <= (textIndex + innerText.length)) {
        tt = innerText.substring(0, replaceStartIndex - textIndex);
        tt += `<span class="bg-emerald-200 rounded bg-opacity-50">${ innerText.substring(replaceStartIndex - textIndex, replaceEndIndex - textIndex) }</span>`;
        const remainText = innerText.substring(replaceEndIndex - textIndex);
        const newReplaceStartIndex = wrapText.indexOf(t, replaceEndIndex);
        const newReplaceEndIndex = newReplaceStartIndex >= 0 ? newReplaceStartIndex + t.length : -1;
        if (remainText && newReplaceStartIndex >= 0 && newReplaceEndIndex >= textIndex && newReplaceStartIndex <= (textIndex + innerText.length)) {
          tt += innerText.substring(replaceEndIndex - textIndex, newReplaceStartIndex - textIndex);
          tt += `<span class="bg-emerald-200 rounded bg-opacity-50">${ innerText.substring(newReplaceStartIndex - textIndex, newReplaceEndIndex - textIndex) }</span>`;
          tt += innerText.substring(newReplaceEndIndex - textIndex);
        } else {
          tt += remainText;
        }
      } else {
        tt = innerText;
      }
      if (ele.childNodes[x].nodeName === '#text') {
        newText += tt;
      } else {
        ele.childNodes[x].innerHTML = tt;
        newText += ele.childNodes[x].outerHTML;
      }
      textIndex += innerText.length;
      if (replaceEndIndex < (textIndex + 1)) {
        replaceStartIndex = wrapText.indexOf(t, replaceEndIndex);
        replaceEndIndex = replaceStartIndex >= 0 ? replaceStartIndex + t.length : -1;
      }
    }
  } else {
    newText = bodyText;
  }
  return newText;
};
</script>
