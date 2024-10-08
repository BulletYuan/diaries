function A(r) {
    const wuxing = {
        '0': '木',
        '1': '火',
        '2': '土',
        '3': '金',
        '4': '水'
    };
    const jiugong = [
    {
        name: '大安',
        bagua: '震',
        wuxing: wuxing['0'],
        desc: '长期、缓慢、稳定；求安稳，大安最吉；求变化，大安不吉'
    },
    {
        name: '留连',
        bagua: '巽',
        wuxing: wuxing['0'],
        desc: '停止、反复、复杂；想挽留，留连是吉；否则剪不断理还乱'
    },
    {
        name: '速喜',
        bagua: '离',
        wuxing: wuxing['1'],
        desc: '惊喜、快速、突然；意想不到的好事'
    },
    {
        name: '赤口',
        bagua: '兑',
        wuxing: wuxing['3'],
        desc: '争斗、凶恶、伤害；吵架、打架、斗争、诉讼是非；肉体伤害（尤其是赤口叠现）'
    },
    {
        name: '小吉',
        bagua: '坎',
        wuxing: wuxing['4'],
        desc: '起步、不多、尚可；成中有缺，适合起步'
    },
    {
        name: '空亡',
        bagua: '中',
        wuxing: wuxing['2'],
        desc: '失去、虚伪、空想；先得再失，尤其金钱事；可多接触玄学、哲学'
    },
    {
        name: '病符',
        bagua: '坤',
        wuxing: wuxing['2'],
        desc: '病态、异常、治疗；先有病，才需要治；整个过程不会太顺利'
    },
    {
        name: '桃花',
        bagua: '艮',
        wuxing: wuxing['2'],
        desc: '欲望、牵绊、异性；人际关系，牵绊此事；除恋爱事之外，桃花都不太好'
    },
    {
        name: '天德',
        bagua: '乾',
        wuxing: wuxing['3'],
        desc: '贵人、上司、高远；求人办事，靠人成事'
    },
    ]
    const total = jiugong.length;

    if (!Array.isArray(r))
        r = [Math.ceil(Math.random() * 999 + 1), Math.ceil(Math.random() * 999 + 1), Math.ceil(Math.random() * 999 + 1)];

    const [c1, c2, c3] = r;
    const result = [
    jiugong[(c1 - 1) % total],
    jiugong[(c1 + c2 - 2) % total],
    jiugong[(c1 + c2 + c3 - 3) % total],
    ];
    return result
}
A()
