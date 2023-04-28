<%*
const dv = app.plugins.plugins["dataview"].api;
var content = tp.file.content;
let dvPage = dv.page(tp.file.title);

async function ratingStars() {
  var displayArr = [];
  var ratingArr = [];

  // Populate Array
  for (let i = 5; i >= 0; i = i - 0.25) {
    ratingArr.push(i);

    if (i <= 1.25) {
      displayArr.push("✨ " + i)
    } else if
      (i > 1.25 && i <= 2.5) {
      displayArr.push("💫 " + i)
    } else if
      (i > 2.5 && i <= 3.75) {
      displayArr.push("⭐️ " + i)
    } else if
      (i > 3.75 && i <= 5) {
      displayArr.push("🌟 " + i)
    }

  }

  return await tp.system.suggester(displayArr, ratingArr);
}

function append(place, add) {
  let find = place;
  let replace = place + "\n" + add;
  content = content.replace(find, replace);
}

function replaceField(field, newValue) {
  let find = new RegExp(`\\[${field}:: .*?\\]`, 'g');
  let replace = `[${field}:: ${newValue}]`;

  content = content.replace(find, replace);
}

async function stageRead() {
  // STATUS
  replaceField("status", "read");
  replaceField("read", tp.date.now("YYYY-MM-DD"));
  content = content.replace("\n#currently-reading", "");

  // RATING
  let rating = await ratingStars();
  replaceField("rating", rating);

  // SUMMARY
  append("***", "\n## Summary/Reflection\n");

  // Write content
  const tFile = tp.file.find_tfile(tp.file.title);
  const newContent = content;
  await app.vault.modify(tFile, newContent);
}
// DO
stageRead();

%>