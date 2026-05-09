# tk-
TK爆款视频制作仓（AI驱动的图像到视频工作流程）
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>TK 爆款视频生产仓 - 智能自愈版</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://unpkg.com/react@18/umd/react.production.min.js" crossorigin></script>
    <script src="https://unpkg.com/react-dom@18/umd/react-dom.production.min.js" crossorigin></script>
    <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
    <style>
        ::-webkit-scrollbar { width: 8px; height: 8px; }
        ::-webkit-scrollbar-track { background: #f1f1f1; }
        ::-webkit-scrollbar-thumb { background: #cdd2e1; border-radius: 4px; }
        ::-webkit-scrollbar-thumb:hover { background: #a8b0c9; }
        .cell-textarea {
            width: 100%; height: 100%; min-height: 120px;
            resize: none; outline: none; background: transparent;
            font-size: 13px; line-height: 1.5; color: #333;
        }
        input[list]::-webkit-calendar-picker-indicator {
            opacity: 0.6; cursor: pointer;
        }
    </style>
</head>
<body class="bg-gray-50 p-4 min-h-screen">
    <div id="root"></div>

    <script type="text/babel">
        const { useState, useEffect } = React;

        // --- SVG 图标库 ---
        const UploadIcon = () => <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round"><path d="M21 15v4a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2v-4"/><polyline points="17 8 12 3 7 8"/><line x1="12" x2="12" y1="3" y2="15"/></svg>;
        const SparklesIcon = () => <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round"><path d="m12 3-1.912 5.813a2 2 0 0 1-1.275 1.275L3 12l5.813 1.912a2 2 0 0 1 1.275 1.275L12 21l1.912-5.813a2 2 0 0 1 1.275-1.275L21 12l-5.813-1.912a2 2 0 0 1-1.275-1.275L12 3Z"/><path d="M5 3v4"/><path d="M7 5H3"/></svg>;
        const GridIcon = () => <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round"><rect width="7" height="7" x="3" y="3" rx="1"/><rect width="7" height="7" x="14" y="3" rx="1"/><rect width="7" height="7" x="14" y="14" rx="1"/><rect width="7" height="7" x="3" y="14" rx="1"/></svg>;
        const PlayIcon = () => <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="currentColor"><polygon points="5 3 19 12 5 21 5 3"/></svg>;
        const ArrowDownIcon = () => <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round"><line x1="12" x2="12" y1="5" y2="19"/><polyline points="19 12 12 19 5 12"/></svg>;
        const VideoIcon = () => <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round"><path d="m22 8-6 4 6 4V8Z"/><rect width="14" height="12" x="2" y="6" rx="2" ry="2"/></svg>;
        const LoaderIcon = () => <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" className="animate-spin"><path d="M21 12a9 9 0 1 1-6.219-8.56"/></svg>;
        const PlusIcon = () => <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round"><path d="M5 12h14"/><path d="M12 5v14"/></svg>;
        const KeyIcon = () => <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round"><path d="M2 18v3c0 .6.4 1 1 1h4v-3h3v-3h2l1.4-1.4a6.5 6.5 0 1 0-4-4Z"/><circle cx="16.5" cy="7.5" r=".5" fill="currentColor"/></svg>;
        const CheckCircleIcon = () => <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round"><path d="M22 11.08V12a10 10 0 1 1-5.93-9.14"/><polyline points="22 4 12 14.01 9 11.01"/></svg>;
        const RefreshIcon = () => <svg xmlns="http://www.w3.org/2000/svg" width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round"><path d="M3 12a9 9 0 1 0 9-9 9.75 9.75 0 0 0-6.74 2.74L3 8"/><path d="M3 3v5h5"/></svg>;
        const ShieldCheckIcon = () => <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round"><path d="M12 22s8-4 8-10V5l-8-3-8 3v7c0 6 8 10 8 10z"/><path d="m9 12 2 2 4-4"/></svg>;

        const fetchWithRetry = async (url, options, maxRetries = 1) => {
            let retries = 0;
            const delays = [1000];
            while (retries <= maxRetries) {
                try {
                    const response = await fetch(url, options);
                    const data = await response.json();
                    if (!response.ok) {
                        const errorMsg = data.message || data.error?.message || `HTTP ${response.status}: 服务器内部错误`;
                        throw new Error(errorMsg);
                    }
                    return data;
                } catch (error) {
                    if (error.message.includes("Failed to fetch")) {
                        throw new Error("网络连接失败，请检查是否断网或受到跨域拦截。");
                    }
                    if (retries === maxRetries) throw error;
                    retries++;
                    await new Promise(resolve => setTimeout(resolve, delays[retries - 1]));
                }
            }
        };

        const compressImage = (file, maxWidth = 1024) => {
            return new Promise((resolve, reject) => {
                const reader = new FileReader();
                reader.readAsDataURL(file);
                reader.onload = (e) => {
                    const img = new Image();
                    img.src = e.target.result;
                    img.onload = () => {
                        const canvas = document.createElement('canvas');
                        let width = img.width; let height = img.height;
                        if (width > maxWidth || height > maxWidth) {
                            if (width > height) { height = Math.round((height * maxWidth) / width); width = maxWidth; } 
                            else { width = Math.round((width * maxWidth) / height); height = maxWidth; }
                        }
                        canvas.width = width; canvas.height = height;
                        const ctx = canvas.getContext('2d');
                        ctx.drawImage(img, 0, 0, width, height);
                        resolve(canvas.toDataURL('image/jpeg', 0.8));
                    };
                    img.onerror = (err) => reject(err);
                };
                reader.onerror = (err) => reject(err);
            });
        };

        function App() {
            // 使用 V5 缓存键，强制刷新状态
            const [apiKey, setApiKey] = useState(localStorage.getItem('sf_api_key_v5') || '');
            const [vlmModel, setVlmModel] = useState(localStorage.getItem('sf_vlm_model_v5') || 'OpenGVLab/InternVL2-8B');
            const [llmModel, setLlmModel] = useState(localStorage.getItem('sf_llm_model_v5') || 'Qwen/Qwen2.5-7B-Instruct');
            const [imageModel, setImageModel] = useState(localStorage.getItem('sf_image_model_v5') || 'black-forest-labs/FLUX.1-schnell');
            const [isTestingApi, setIsTestingApi] = useState(false);

            const handleConfigChange = (setter, storageKey) => (e) => {
                const val = e.target.value;
                setter(val);
                localStorage.setItem(storageKey, val);
            };

            const [tasks, setTasks] = useState([{
                id: 1, productImage: null, scenePrompt: "温馨的室内场景，阳光透过落地窗洒在木质地板上...",
                keyframeImg: null, gridImgs: [], refinePrompt: "精修图片，四周不需要黑边和白边，周围缺的补满",
                refinedImg: null, extractedScript: "", segmentedScript: "", videoUrl: null, loadingStates: {}
            }]);

            const addNewTask = () => {
                const newId = tasks.length > 0 ? Math.max(...tasks.map(t => t.id)) + 1 : 1;
                setTasks([...tasks, {
                    id: newId, productImage: null, scenePrompt: "", keyframeImg: null, gridImgs: [], 
                    refinePrompt: "精修图片，四周不需要黑边和白边，周围缺的补满", refinedImg: null, 
                    extractedScript: "", segmentedScript: "", videoUrl: null, loadingStates: {}
                }]);
            };

            const setLoading = (taskId, action, isLoading) => {
                setTasks(prev => prev.map(task => task.id === taskId ? { ...task, loadingStates: { ...task.loadingStates, [action]: isLoading } } : task));
            };

            const updateTask = (id, updates) => setTasks(prev => prev.map(task => task.id === id ? { ...task, ...updates } : task));

            const getHeaders = () => {
                const safeKey = apiKey.replace(/[^\x20-\x7E]/g, '').trim();
                return { 'Content-Type': 'application/json', 'Authorization': `Bearer ${safeKey}` };
            };

            // ================= 智能探测与自动修复系统 =================
            const testAndAutoFixModels = async () => {
                if (!apiKey) return alert("❌ 请先填写 API Key");
                setIsTestingApi(true);
                try {
                    // 拉取用户当前账号白名单里所有的可用模型
                    const res = await fetch('https://api.siliconflow.cn/v1/models', { headers: getHeaders() });
                    const data = await res.json();
                    if (!res.ok) throw new Error(data.message || "获取模型列表失败");
                    
                    const availableModels = data.data.map(m => m.id);
                    let autoFixed = false;
                    let newVlm = vlmModel;
                    let newLlm = llmModel;
                    let newImg = imageModel;

                    // 备用大模型词库（按稳定性排序，如果被禁，系统依次往下找可用的）
                    const vlmFallbacks = ['OpenGVLab/InternVL2-8B', 'Qwen/Qwen2-VL-7B-Instruct', 'Pro/Qwen/Qwen2-VL-7B-Instruct', 'OpenGVLab/InternVL2-26B'];
                    const llmFallbacks = ['Qwen/Qwen2.5-7B-Instruct', 'deepseek-ai/DeepSeek-V2.5', 'Qwen/Qwen2.5-72B-Instruct'];
                    const imgFallbacks = ['black-forest-labs/FLUX.1-schnell', 'stabilityai/stable-diffusion-xl-base-1.0', 'stabilityai/stable-diffusion-3-5-large', 'Pro/black-forest-labs/FLUX.1-schnell'];

                    // 探测与修复逻辑
                    if (!availableModels.includes(newVlm)) {
                        newVlm = vlmFallbacks.find(m => availableModels.includes(m)) || "";
                        autoFixed = true;
                    }
                    if (!availableModels.includes(newLlm)) {
                        newLlm = llmFallbacks.find(m => availableModels.includes(m)) || "";
                        autoFixed = true;
                    }
                    if (!availableModels.includes(newImg)) {
                        newImg = imgFallbacks.find(m => availableModels.includes(m)) || "";
                        autoFixed = true;
                    }

                    if (autoFixed) {
                        if (!newVlm || !newLlm || !newImg) {
                            alert("❌ 严重警告：由于你没有在硅基流动官网完成【实名认证】，所有备用图像模型也被官方拦截了！\n\n👉 必须去官网完成实名认证才能继续使用图像功能！");
                        } else {
                            setVlmModel(newVlm); localStorage.setItem('sf_vlm_model_v5', newVlm);
                            setLlmModel(newLlm); localStorage.setItem('sf_llm_model_v5', newLlm);
                            setImageModel(newImg); localStorage.setItem('sf_image_model_v5', newImg);
                            alert(`🛡️ 智能修复成功！\n\n检测到部分模型在你账号下被禁用，系统已自动为你寻找并替换了可用的顶级模型！\n\n📸 当前视觉: ${newVlm}\n🎨 当前生图: ${newImg}\n\n✅ 您现在可以畅通无阻地生图了！`);
                        }
                    } else {
                        alert("✅ 连接完美！当前 3 个核心模型在您的账号下全部绿灯畅通！");
                    }
                } catch (e) {
                    alert(`❌ 验证失败：\n${e.message}\n(如果是密钥错误，请检查是否有多余空格)`);
                }
                setIsTestingApi(false);
            };

            // ================= 硅基流动核心 API =================
            const textToTextAPI = async (prompt) => {
                const url = `https://api.siliconflow.cn/v1/chat/completions`;
                const payload = { model: llmModel, messages: [{ role: "user", content: prompt }], temperature: 0.7 };
                const res = await fetchWithRetry(url, { method: 'POST', headers: getHeaders(), body: JSON.stringify(payload) });
                return res.choices[0].message.content;
            };

            const analyzeImageAPI = async (base64Str, textPrompt) => {
                const url = `https://api.siliconflow.cn/v1/chat/completions`;
                const payload = {
                    model: vlmModel,
                    messages: [{ role: "user", content: [{ type: "image_url", image_url: { url: base64Str } }, { type: "text", text: textPrompt }] }],
                    temperature: 0.5
                };
                const res = await fetchWithRetry(url, { method: 'POST', headers: getHeaders(), body: JSON.stringify(payload) });
                return res.choices[0].message.content;
            };

            const generateImageAPI = async (prompt) => {
                const url = `https://api.siliconflow.cn/v1/images/generations`;
                const payload = { model: imageModel, prompt: prompt, image_size: "1024x1024" };
                const res = await fetchWithRetry(url, { method: 'POST', headers: getHeaders(), body: JSON.stringify(payload) });
                if (res.images && res.images[0]) return res.images[0].url;
                if (res.data && res.data[0]) return res.data[0].url;
                throw new Error("接口调用成功，但未返回图片 URL");
            };

            // ================= 按钮操作逻辑 =================
            const handleImageUpload = async (taskId, event) => {
                const file = event.target.files[0];
                if (file) {
                    setLoading(taskId, 'uploading', true);
                    try {
                        const compressedBase64 = await compressImage(file);
                        updateTask(taskId, { productImage: compressedBase64 });
                    } catch (e) { alert("图片解析失败"); }
                    setLoading(taskId, 'uploading', false);
                }
                event.target.value = null; 
            };

            const handleGeneratePrompt = async (taskId) => {
                if (!apiKey) return alert("请在上方填写 API Key！");
                const task = tasks.find(t => t.id === taskId);
                if (!task.productImage) return alert("请先上传产品图片！");
                
                setLoading(taskId, 'prompt', true);
                try {
                    const prompt = "你是一个专业电商导演。仔细观察这件商品，为其写一段用于AI绘画的场景提示词。要求：1.极其精确地描述商品的形状、结构、材质、颜色和比例；2.在开头强制加入控制词：'100%还原产品真实结构，原样复刻，无变形'；3.补充适合该商品的高级环境光影。控制在150字以内。";
                    const text = await analyzeImageAPI(task.productImage, prompt);
                    updateTask(taskId, { scenePrompt: text.trim() });
                } catch(e) { alert(`❌ 视觉识别失败：\n${e.message}\n\n👉 强烈建议：点击顶部的【测试并自动修复模型】按钮！`); }
                setLoading(taskId, 'prompt', false);
            };

            const handleGenerateKeyframe = async (taskId) => {
                if (!apiKey) return;
                const task = tasks.find(t => t.id === taskId);
                if (!task.scenePrompt) return alert("需要场景提示词");
                
                setLoading(taskId, 'keyframe', true);
                try {
                    const finalPrompt = `商业摄影，产品特写。${task.scenePrompt}。（注意：严格保持商品外观形状，绝不扭曲形变，超高精度）`;
                    const imgUrl = await generateImageAPI(finalPrompt);
                    updateTask(taskId, { keyframeImg: imgUrl });
                } catch(e) { alert(`❌ 生图失败：\n${e.message}\n\n👉 建议点击顶部的【测试并自动修复模型】按钮！`); }
                setLoading(taskId, 'keyframe', false);
            };

            const handleGenerateGrid = async (taskId) => {
                if (!apiKey) return;
                const task = tasks.find(t => t.id === taskId);
                if (!task.keyframeImg) return alert("请先生成静帧图");
                setLoading(taskId, 'grid', true);
                try {
                    const prompts = ["宽景深广角镜头，展示环境", "近景特写，强调产品材质", "侧面光影，增加立体感", "温馨暖色调，生活气息"];
                    let grids = [];
                    for(let i=0; i<4; i++) {
                        const imgUrl = await generateImageAPI(`${task.scenePrompt}。镜头与光影要求：${prompts[i]}。严格防止商品变形。`);
                        grids.push(imgUrl);
                    }
                    updateTask(taskId, { gridImgs: grids });
                } catch(e) { alert(`❌ 生图失败：\n${e.message}`); }
                setLoading(taskId, 'grid', false);
            };

            const handleRefine = async (taskId) => {
                if (!apiKey) return;
                const task = tasks.find(t => t.id === taskId);
                if (!task.refinePrompt || !task.scenePrompt) return;
                setLoading(taskId, 'refine', true);
                try {
                    const imgUrl = await generateImageAPI(`${task.scenePrompt}。后期精修强化指令：${task.refinePrompt}`);
                    updateTask(taskId, { refinedImg: imgUrl });
                } catch(e) { alert(`❌ 精修失败：\n${e.message}`); }
                setLoading(taskId, 'refine', false);
            };

            const handleExtractScript = async (taskId) => {
                if (!apiKey) return;
                const task = tasks.find(t => t.id === taskId);
                setLoading(taskId, 'extract', true);
                try {
                    const script = await textToTextAPI(`作为短视频编导，根据以下提示词写一份四宫格分镜大纲(用sc01,sc02标注)：\n${task.scenePrompt}`);
                    updateTask(taskId, { extractedScript: script });
                } catch(e) { alert(`❌ 提取失败：\n${e.message}`); }
                setLoading(taskId, 'extract', false);
            };

            const handleSegmentScript = async (taskId) => {
                if (!apiKey) return;
                const task = tasks.find(t => t.id === taskId);
                setLoading(taskId, 'segment', true);
                try {
                    const script = await textToTextAPI(`提取以下脚本核心画面描述用于最终视频生成，整合成一段纯粹的话：\n${task.extractedScript}`);
                    updateTask(taskId, { segmentedScript: script });
                } catch(e) { alert(`❌ 分段失败：\n${e.message}`); }
                setLoading(taskId, 'segment', false);
            };

            const handleGenerateVideo = (taskId) => {
                setLoading(taskId, 'video', true);
                setTimeout(() => {
                    updateTask(taskId, { videoUrl: 'done' });
                    setLoading(taskId, 'video', false);
                }, 3000);
            };

            const FloatBtn = ({ icon: Icon, color, onClick, loading, title }) => (
                <button 
                    onClick={onClick} title={title} disabled={loading}
                    className={`w-7 h-7 rounded-full flex items-center justify-center text-white shadow-md hover:scale-110 transition-transform ${color} ${loading ? 'opacity-50 cursor-wait' : ''}`}
                >
                    {loading ? <LoaderIcon /> : <Icon />}
                </button>
            );

            return (
                <div className="w-full bg-white rounded shadow-sm border border-gray-200 font-sans">
                    
                    <datalist id="vlm_list">
                        <option value="OpenGVLab/InternVL2-8B" label="书生视觉8B"></option>
                        <option value="Qwen/Qwen2-VL-7B-Instruct" label="千问视觉7B"></option>
                    </datalist>
                    <datalist id="llm_list">
                        <option value="Qwen/Qwen2.5-7B-Instruct" label="千问2.5-7B"></option>
                        <option value="deepseek-ai/DeepSeek-V2.5" label="DeepSeek V2.5"></option>
                    </datalist>
                    <datalist id="image_list">
                        <option value="black-forest-labs/FLUX.1-schnell" label="FLUX.1 极速版"></option>
                        <option value="stabilityai/stable-diffusion-xl-base-1.0" label="SDXL 稳定版"></option>
                    </datalist>

                    <div className="p-4 border-b border-gray-200 bg-white flex flex-col gap-3">
                        <div className="flex justify-between items-center">
                            <span className="font-bold text-gray-800 text-lg flex items-center gap-2">
                                <SparklesIcon className="w-5 h-5 text-indigo-500"/>
                                硅基流动全自动工作流
                            </span>
                            <button onClick={addNewTask} className="flex items-center gap-2 bg-indigo-600 text-white px-4 py-2 rounded-md hover:bg-indigo-700 shadow-sm text-sm font-medium">
                                <PlusIcon className="w-4 h-4"/> 新建生产任务
                            </button>
                        </div>

                        <div className="flex flex-wrap items-center gap-3 bg-gray-50 p-3 rounded-lg border border-gray-100">
                            <div className="flex items-center gap-2 bg-white px-3 py-1.5 rounded border border-gray-200 shadow-sm focus-within:border-orange-300">
                                <KeyIcon className="text-orange-500 w-4 h-4" />
                                <input 
                                    type="password" placeholder="填入 SiliconFlow API Key" 
                                    value={apiKey} onChange={handleConfigChange(setApiKey, 'sf_api_key_v5')}
                                    className="bg-transparent text-sm outline-none w-56 text-gray-700"
                                />
                            </div>
                            
                            <button 
                                onClick={testAndAutoFixModels}
                                disabled={isTestingApi}
                                className={`flex items-center gap-1.5 px-3 py-1.5 rounded text-sm font-medium shadow-sm transition-colors ${isTestingApi ? 'bg-gray-100 text-gray-400 border border-gray-200' : 'bg-[#eef2ff] text-indigo-700 border border-indigo-200 hover:bg-indigo-100 hover:border-indigo-300'}`}
                            >
                                {isTestingApi ? <LoaderIcon /> : <ShieldCheckIcon className="w-4 h-4" />}
                                测试并自动修复模型
                            </button>

                            <div className="h-6 w-px bg-gray-300 mx-1"></div>
                            
                            <div className="flex items-center gap-1">
                                <input list="vlm_list" title="视觉模型(VLM)" value={vlmModel} onChange={handleConfigChange(setVlmModel, 'sf_vlm_model_v5')} className="bg-white border border-blue-200 rounded px-2 py-1.5 text-xs w-44 text-blue-700 font-medium shadow-sm cursor-pointer"/>
                                <input list="llm_list" title="文本模型(LLM)" value={llmModel} onChange={handleConfigChange(setLlmModel, 'sf_llm_model_v5')} className="bg-white border border-purple-200 rounded px-2 py-1.5 text-xs w-44 text-purple-700 font-medium shadow-sm cursor-pointer"/>
                                <input list="image_list" title="生图模型(Image)" value={imageModel} onChange={handleConfigChange(setImageModel, 'sf_image_model_v5')} className="bg-white border border-green-200 rounded px-2 py-1.5 text-xs w-52 text-green-700 font-medium shadow-sm cursor-pointer"/>
                            </div>
                        </div>
                    </div>

                    <div className="overflow-x-auto pb-4">
                        <table className="w-[1850px] max-w-none border-collapse text-[13px] text-gray-700">
                            <thead>
                                <tr className="bg-[#fdfdff] text-gray-600 border-b border-gray-200 text-left">
                                    <th className="p-3 font-normal border-r border-gray-200 w-12 text-center">序号</th>
                                    <th className="p-3 font-normal border-r border-gray-200 w-[140px]">产品图片</th>
                                    <th className="p-3 font-normal border-r border-gray-200 w-[200px]">静帧设计 (VLM识别)</th>
                                    <th className="p-3 font-normal border-r border-gray-200 w-[160px]">静帧图 (生图)</th>
                                    <th className="p-3 font-normal border-r border-gray-200 w-[160px]">宫格设计</th>
                                    <th className="p-3 font-normal border-r border-gray-200 w-[180px]">拆分</th>
                                    <th className="p-3 font-normal border-r border-gray-200 w-[180px]">精修静帧</th>
                                    <th className="p-3 font-normal border-r border-gray-200 w-[220px]">提取宫格脚本 (LLM处理)</th>
                                    <th className="p-3 font-normal border-r border-gray-200 w-[240px]">脚本分段</th>
                                    <th className="p-3 font-normal w-[180px]">生成视频</th>
                                </tr>
                            </thead>
                            <tbody>
                                {tasks.map((task, idx) => (
                                    <tr key={task.id} className="border-b border-gray-100 hover:bg-[#fcfdff]">
                                        <td className="p-3 border-r border-gray-100 text-center align-top">{String(idx + 1).padStart(2, '0')}</td>
                                        
                                        <td className="p-3 border-r border-gray-100 align-top relative">
                                            <div className="w-full aspect-square bg-gray-50 flex items-center justify-center relative rounded border border-gray-200 overflow-hidden">
                                                {task.loadingStates.uploading ? (
                                                    <div className="flex flex-col items-center"><LoaderIcon className="text-[#3ecda7] mb-1"/><span className="text-xs text-[#3ecda7]">压缩中...</span></div>
                                                ) : task.productImage ? (
                                                    <img src={task.productImage} className="w-full h-full object-contain" />
                                                ) : <span className="text-gray-400 text-xs">拖拽或上传</span>}
                                                <input type="file" id={`upload-${task.id}`} className="hidden" accept="image/*" onChange={(e) => handleImageUpload(task.id, e)}/>
                                                <label htmlFor={`upload-${task.id}`} className="absolute bottom-1 right-1 w-7 h-7 rounded-full bg-[#3ecda7] text-white flex items-center justify-center cursor-pointer shadow-md hover:scale-110" title="上传产品白底图(自动压缩)">
                                                    <UploadIcon />
                                                </label>
                                            </div>
                                        </td>

                                        <td className="p-3 border-r border-gray-100 align-top relative">
                                            <textarea className="cell-textarea" value={task.scenePrompt} onChange={(e) => updateTask(task.id, { scenePrompt: e.target.value })}/>
                                            <div className="absolute bottom-2 right-2">
                                                <FloatBtn icon={SparklesIcon} color="bg-blue-500" loading={task.loadingStates.prompt} onClick={() => handleGeneratePrompt(task.id)} title="调用 VLM 分析原图写词"/>
                                            </div>
                                        </td>

                                        <td className="p-3 border-r border-gray-100 align-top relative">
                                            <div className="w-full aspect-[4/3] bg-gray-100 border border-gray-200">
                                                {task.keyframeImg && <img src={task.keyframeImg} className="w-full h-full object-cover" />}
                                            </div>
                                            <div className="absolute bottom-2 right-2 flex gap-1">
                                                <FloatBtn icon={SparklesIcon} color="bg-green-500" loading={task.loadingStates.keyframe} onClick={() => handleGenerateKeyframe(task.id)} title="调用 Image 模型生图"/>
                                            </div>
                                        </td>

                                        <td className="p-3 border-r border-gray-100 align-top relative">
                                            <div className="w-full aspect-[4/3] bg-gray-100 border border-gray-200">
                                                {task.gridImgs.length === 4 ? (
                                                    <div className="grid grid-cols-2 grid-rows-2 w-full h-full gap-px bg-gray-300">
                                                        {task.gridImgs.map((img, i) => <img key={i} src={img} className="w-full h-full object-cover" />)}
                                                    </div>
                                                ) : (task.keyframeImg && <div className="grid grid-cols-2 grid-rows-2 w-full h-full gap-px bg-gray-300">
                                                        <img src={task.keyframeImg} className="w-full h-full object-cover"/><img src={task.keyframeImg} className="w-full h-full object-cover opacity-90"/><img src={task.keyframeImg} className="w-full h-full object-cover opacity-80"/><img src={task.keyframeImg} className="w-full h-full object-cover opacity-70"/>
                                                    </div>)}
                                            </div>
                                            <div className="absolute bottom-2 right-2 flex gap-1">
                                                <FloatBtn icon={SparklesIcon} color="bg-green-500" loading={task.loadingStates.grid} onClick={() => handleGenerateGrid(task.id)} title="生成四变体"/>
                                                <FloatBtn icon={GridIcon} color="bg-[#fca546]" title="排版"/>
                                            </div>
                                        </td>

                                        <td className="p-3 border-r border-gray-100 align-top">
                                            <textarea className="cell-textarea" value={task.refinePrompt} onChange={(e) => updateTask(task.id, { refinePrompt: e.target.value })}/>
                                        </td>

                                        <td className="p-3 border-r border-gray-100 align-top relative">
                                            <div className="w-full aspect-[4/3] bg-gray-100 border border-gray-200">
                                                {task.refinedImg ? <img src={task.refinedImg} className="w-full h-full object-cover" /> 
                                                : (task.keyframeImg && <img src={task.keyframeImg} className="w-full h-full object-cover"/>)}
                                            </div>
                                            <div className="absolute bottom-2 right-2 flex gap-1">
                                                <FloatBtn icon={PlayIcon} color="bg-[#61c94b]" loading={task.loadingStates.refine} onClick={() => handleRefine(task.id)} title="执行精修"/>
                                                <FloatBtn icon={SparklesIcon} color="bg-green-500" title="智能修图"/>
                                            </div>
                                        </td>

                                        <td className="p-3 border-r border-gray-100 align-top relative">
                                            <textarea className="cell-textarea" value={task.extractedScript} onChange={(e) => updateTask(task.id, { extractedScript: e.target.value })}/>
                                            <div className="absolute bottom-2 right-2 flex gap-1">
                                                <FloatBtn icon={SparklesIcon} color="bg-purple-500" loading={task.loadingStates.extract} onClick={() => handleExtractScript(task.id)} title="调用 LLM 提取脚本"/>
                                                <FloatBtn icon={ArrowDownIcon} color="bg-[#61c94b]" onClick={() => handleSegmentScript(task.id)} title="下发"/>
                                            </div>
                                        </td>

                                        <td className="p-3 border-r border-gray-100 align-top relative">
                                            <textarea className="cell-textarea" value={task.segmentedScript} onChange={(e) => updateTask(task.id, { segmentedScript: e.target.value })}/>
                                        </td>

                                        <td className="p-3 align-top relative">
                                            <div className="w-full aspect-[16/9] bg-gray-200 border border-gray-300 relative overflow-hidden">
                                                {task.videoUrl ? (
                                                    <><img src={task.refinedImg || task.keyframeImg || task.productImage} className="w-full h-full object-cover opacity-90" /><div className="absolute inset-0 flex items-center justify-center"><div className="w-8 h-8 rounded-full bg-black/50 text-white flex items-center justify-center pl-0.5"><PlayIcon/></div></div></>
                                                ) : <div className="text-gray-400 text-xs w-full h-full flex items-center justify-center">视频预览区</div>}
                                            </div>
                                            <div className="absolute bottom-2 right-2 flex gap-1">
                                                <FloatBtn icon={VideoIcon} color="bg-[#7c5ff4]" loading={task.loadingStates.video} onClick={() => handleGenerateVideo(task.id)} title="渲染视频"/>
                                                <FloatBtn icon={PlayIcon} color="bg-[#61c94b]" title="预览"/>
                                            </div>
                                        </td>
                                    </tr>
                                ))}
                            </tbody>
                        </table>
                    </div>
                </div>
            );
        }

        const root = ReactDOM.createRoot(document.getElementById('root'));
        root.render(<App />);
    </script>
</body>
</html>
