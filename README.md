---
layout: default
title: 기출문제 정리장
---

## 📝 기출문제 즉시 추가 및 관리

<div style="background-color: #f8f9fa; padding: 20px; border-radius: 8px; border: 1px solid #dee2e6; margin-bottom: 20px;">
  <div style="display: grid; grid-template-columns: repeat(auto-fit, minmax(200px, 1fr)); gap: 15px; margin-bottom: 15px;">
    <div>
      <label style="font-weight: bold; display: block; margin-bottom: 5px;">연도</label>
      <input type="text" id="inputYear" placeholder="예: 2026" style="width: 100%; padding: 8px; border: 1px solid #ccc; border-radius: 4px;">
    </div>
    <div>
      <label style="font-weight: bold; display: block; margin-bottom: 5px;">과목</label>
      <select id="inputSubject" style="width: 100%; padding: 8px; border: 1px solid #ccc; border-radius: 4px;">
        <option value="기초심리평가">기초심리평가</option>
        <option value="기초심리상담">기초심리상담</option>
        <option value="심리치료">심리치료</option>
        <option value="자문교육심리재활">자문교육심리재활</option>
      </select>
    </div>
    <div>
      <label style="font-weight: bold; display: block; margin-bottom: 5px;">추가 태그 (쉼표 분리)</label>
      <input type="text" id="inputTags" placeholder="예: MMPI, 성인, 불안" style="width: 100%; padding: 8px; border: 1px solid #ccc; border-radius: 4px;">
    </div>
  </div>
  
  <div style="margin-bottom: 15px;">
    <label style="font-weight: bold; display: block; margin-bottom: 5px;">문제</label>
    <textarea id="inputQuestion" rows="2" placeholder="문제를 입력하세요" style="width: 100%; padding: 8px; border: 1px solid #ccc; border-radius: 4px; resize: vertical;"></textarea>
  </div>
  
  <div style="margin-bottom: 15px;">
    <label style="font-weight: bold; display: block; margin-bottom: 5px;">답안</label>
    <textarea id="inputAnswer" rows="3" placeholder="답안을 입력하세요" style="width: 100%; padding: 8px; border: 1px solid #ccc; border-radius: 4px; resize: vertical;"></textarea>
  </div>
  
  <button onclick="addProblem()" style="background-color: #007bff; color: white; border: none; padding: 10px 20px; border-radius: 4px; cursor: pointer; font-weight: bold;">+ 목록에 바로 추가</button>
</div>

<hr>

## 📚 문제 목록

<div style="overflow-x: auto;">
  <table style="width: 100%; border-collapse: collapse; margin-top: 10px;">
    <thead>
      <tr style="background-color: #e9ecef; border-bottom: 2px solid #dee2e6;">
        <th style="padding: 12px; text-align: left; width: 8%;">연도</th>
        <th style="padding: 12px; text-align: left; width: 15%;">과목</th>
        <th style="padding: 12px; text-align: left; width: 35%;">문제</th>
        <th style="padding: 12px; text-align: left; width: 30%;">답안</th>
        <th style="padding: 12px; text-align: left; width: 12%;">태그</th>
      </tr>
    </thead>
    <tbody id="problemTableBody">
      <tr id="emptyRow">
        <td colspan="5" style="padding: 20px; text-align: center; color: #6c757d;">등록된 문제가 없습니다. 위에서 첫 문제를 추가해 보세요!</td>
      </tr>
    </tbody>
  </table>
</div>

<script>
  // 브라우저가 닫혀도 데이터가 유지되도록 로컬 스토리지 활용
  let localData = localStorage.getItem('myProblems');
  let problems = localData ? JSON.parse(localData) : [];

  // 초기 로드 시 기존 데이터 렌더링
  document.addEventListener("DOMContentLoaded", function() {
    renderTable();
  });

  // 데이터 추가 함수
  function addProblem() {
    const year = document.getElementById('inputYear').value.trim() || '미정';
    const subject = document.getElementById('inputSubject').value;
    const question = document.getElementById('inputQuestion').value.trim();
    const answer = document.getElementById('inputAnswer').value.trim();
    const tagsInput = document.getElementById('inputTags').value.trim();

    if (!question || !answer) {
      alert('문제와 답안은 반드시 입력해야 합니다.');
      return;
    }

    // 태그 분리 및 정리 (쉼표 기준)
    const tags = tagsInput ? tagsInput.split(',').map(t => t.trim()).filter(t => t !== '') : [];

    // 객체 생성
    const newProblem = { year, subject, question, answer, tags };
    
    // 배열에 추가 및 저장
    problems.push(newProblem);
    localStorage.setItem('myProblems', JSON.stringify(problems));

    // 입력창 초기화 (과목 제외)
    document.getElementById('inputYear').value = '';
    document.getElementById('inputQuestion').value = '';
    document.getElementById('inputAnswer').value = '';
    document.getElementById('inputTags').value = '';

    // 테이블 갱신
    renderTable();
  }

  // 테이블 그리기 함수
  function renderTable() {
    const tbody = document.getElementById('problemTableBody');
    tbody.innerHTML = '';

    if (problems.length === 0) {
      tbody.innerHTML = `
        <tr id="emptyRow">
          <td colspan="5" style="padding: 20px; text-align: center; color: #6c757d;">등록된 문제가 없습니다. 위에서 첫 문제를 추가해 보세요!</td>
        </tr>`;
      return;
    }

    problems.forEach((p, index) => {
      const tr = document.createElement('tr');
      tr.style.borderBottom = "1px solid #dee2e6";
      
      // 태그 배지 생성
      const tagBadges = p.tags.map(t => `<span style="background-color: #e2e3e5; color: #383d41; padding: 2px 6px; border-radius: 4px; font-size: 0.8em; margin-right: 4px; display: inline-block; margin-bottom: 2px;">#${t}</span>`).join('');

      tr.innerHTML = `
        <td style="padding: 12px; vertical-align: top;">${p.year}</td>
        <td style="padding: 12px; vertical-align: top;"><strong style="color: #495057;">${p.subject}</strong></td>
        <td style="padding: 12px; vertical-align: top; white-space: pre-line;">${p.question}</td>
        <td style="padding: 12px; vertical-align: top; white-space: pre-line; color: #155724; background-color: #f4fbf7;">${p.answer}</td>
        <td style="padding: 12px; vertical-align: top;">${tagBadges}</td>
      `;
      tbody.appendChild(tr);
    });
  }
</script>
