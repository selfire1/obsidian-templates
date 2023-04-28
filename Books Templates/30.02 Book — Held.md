<%*
const dv = app.plugins.plugins["dataview"].api;
var content = tp.file.content;
let dvPage = dv.page(tp.file.title);

stageReserved();

async function stageReserved() {
  // STATUS
  await replaceField("status", "held");
  
  // THUMBNAIL
var retVal = confirm("Do you want to replace the thumbnail?");
if (retVal) {
  await replaceThumbnail();
}

// Write content
const tFile = tp.file.find_tfile(tp.file.title);
const newContent = content;
await app.vault.modify(tFile, newContent);
}

async function replaceThumbnail() {
  let url = dvPage.kindle;
  window.open(url);
  let imgLink = await tp.system.prompt("Thumbnail link", "", "");
  await replaceField("thumbnail", imgLink);
  // Replace embed
  await replaceField("thumbnail", imgLink, true);
}

async function replaceField(field, newValue, thumb = false) {
  let find = new RegExp(`\\[${field}:: .*?\\]`,'g');
  let replace = `[${field}:: "${newValue}"]`;
  
  if (thumb) {
  find = new RegExp(`!\\[thumbnail\\|150\\]\\(.*?\\)`,'g');
  replace = `![thumbnail|150](${newValue})`;
  }
  
content = content.replace(find, replace);
}
%>