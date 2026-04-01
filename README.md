# YoungsunLee_Portfolio_GameClientProgrammer_2026

개발자 이영선의 게임 프로젝트 3개를 정리한 저장소입니다.

## 프로젝트 목록
- **Project SV**  
Unity / 2D TopDown / Simulation  
2025.06~2025.11 / Solo  

- **Project HAD**  
Unity / 3D Isometric / Action  
2024.06~2024.12 / Solo

- **Project Spear**  
Unity / 2D Side / Action  
2023.06~2023.11 / Team

## 이미지
- **Project SV**  


- **Project HAD**  


- **Project Spear**  

## 내용
각 프로젝트의 소스 코드를 압축한 파일입니다.

- **스크립트** (source code)  
  (팀 프로젝트의 경우, 담당한 스크립트만 포함합니다.)

## 조작법

<img width="575" height="322" alt="image" src="https://github.com/user-attachments/assets/a0724b87-3420-4f01-9007-0b73b4ce5cd6" />  

이동 **WASD**  
툴바 선택 **Scroll Wheel**  
인벤토리 탭 **E**  
도구 사용 **Left Click**  
상호작용 **Right Click**  
장비 탭 **Q**  
제작 탭 **C**  

<img width="575" alt="image" src="https://github.com/user-attachments/assets/3940fe75-bda3-4667-bbb0-3e9ed74e1d7d" />  

이동 **WASD**
대시 **Space**  
공격 **Left Click**  
원거리 공격 **Right Click**  
특수 공격 **Q**    
상호작용 **E**  
스킬 탭 **B**

<img width="573" height="322" alt="image" src="https://github.com/user-attachments/assets/00c9caa5-ce10-4705-8a31-96be6a32ef14" />  

이동 **A/D**
점프 **Space**  
공격(찌르기) **Left Click**  
공격(던지기) **Right Click**  

## 예시 코드  
ReadMe 최하단에 프로젝트의 일부 스크립트를 발췌했습니다. 코드 스타일 확인 등을 위해 편히 봐주세요.

---

# YoungsunLee_Portfolio_GameClientProgrammer_2026

This repository contains three game projects developed by Youngsun Lee.

## Project List
- **Project SV**  
Unity / 2D TopDown / Simulation  
2025.06~2025.11 / Solo  

- **Project HAD**  
Unity / 3D Isometric / Action  
2024.06~2024.12 / Solo

- **Project Spear**  
Unity / 2D Side / Action  
2023.06~2023.11 / Team

## Images
- **Project SV**  


- **Project HAD**  


- **Project Spear**  

## Contents
Each project includes the following: 

- **Scripts** (source code)  
  (For team projects, only the scripts written by Youngsun Lee are included.)

## Controls

<img width="575" height="322" alt="image" src="https://github.com/user-attachments/assets/a0724b87-3420-4f01-9007-0b73b4ce5cd6" />  

Move **WASD**  
Scroll Toolbar **Scroll Wheel**  
Inventory Tab **E**  
Use Tool/Place Item **Left Click**  
Interaction **Right Click**  
Equipment Tab **Q**  
Craft Tab **C**  

<img width="575" alt="image" src="https://github.com/user-attachments/assets/3940fe75-bda3-4667-bbb0-3e9ed74e1d7d" />  

Move **WASD**
Dash **Space**  
Attack **Left Click**  
Attack(Arrow) **Right Click**  
Attack(Special) **Q**    
Interaction **E**  
Skill Tab **B**

<img width="573" height="322" alt="image" src="https://github.com/user-attachments/assets/00c9caa5-ce10-4705-8a31-96be6a32ef14" />  

Move **A/D**
Jump **Space**  
Attack(Pierce) **Left Click**  
Attack(Throw) **Right Click**  

## Sample Code  
Selected scripts from the projects are included at the bottom of this README to provide an overview of the code style and implementation details.

- **Project SV' TimeAgent & PlacedObjectTickManager**
public class TimeAgent : MonoBehaviour
{
    public Action onTimeTick;

    protected virtual void Start()
    {
        Init();
    }

    protected virtual void OnDestroy()
    {
        TimeManager.Singleton.UnSubscribe(this);
    }

    public void Init()
    {
        TimeManager.Singleton.Subscribe(this);
    }

    public void InvokeTick()
    {
        onTimeTick?.Invoke();
    }
}

public class PlacedObjectTickManager : TimeAgent
{
    protected override void Start()
    {
        base.Start();
        onTimeTick += Tick;
    }

    protected override void OnDestroy()
    {
        base.OnDestroy();
        onTimeTick -= Tick;
    }

    public void Tick()
    {
        if (PlaceableObjectsManager.Singleton.Container == null) return;

        foreach (PlacedItem item in PlaceableObjectsManager.Singleton.Container.PlacedItems)
        {
            if (item == null) continue;
            if (item.ConvertorData == null) continue;
            if (!item.ConvertorData.IsConverting) continue;

            item.ConvertorData.TickConvertingTimer(1);

            if (item.ConvertorData.CurrentTimer >= item.ConvertorData.FullTimer)
            {
                item.ConvertorData.SetIsConverting(false);
                item.ConvertorData.SetIsConvertingOver(true);
            }
        }
    }
}

- **Project SV' CropTile & CropManager**
[Serializable]
public class CropTile
{
    public Vector3Int Position => position;
    public int CurrentGrowthTimer => currentGrowthTimer;
    public CropData CropData => cropData;
    public int CurrentGrowthStage => currentGrowthStage;
    public bool IsMature => isMature;
    public SpriteRenderer Renderer => renderer;

    [SerializeField] private Vector3Int position;
    [SerializeField] private int currentGrowthTimer;
    [SerializeField] private CropData cropData;
    [SerializeField] private int currentGrowthStage;
    [SerializeField] private bool isMature;
    [SerializeField] private SpriteRenderer renderer;

    public void SetRenderer(SpriteRenderer rd) { renderer = rd; }
    public void SetSprite(Sprite sprite) { renderer.sprite = sprite; }
    public void TickGrowthTimer(int time) { this.currentGrowthTimer += time; }
    public void SetCrop(CropData crop) { this.cropData = crop; }
    public void TickGrowthStage(int stage) { this.currentGrowthStage += stage; }
    public void SetIsMature(bool tf) { this.isMature = tf; }

    public CropTile(CropData _crop, Vector3Int _pos, int _timer = 0, int _stage = 0) { cropData = _crop; position = _pos; currentGrowthTimer = _timer; currentGrowthStage = _stage; }

    public void ResetCropTile()
    {
        cropData = null;
        currentGrowthTimer = 0;
        currentGrowthStage = 0;
        isMature = false;
        renderer = null;
    }
}

public class CropManager : SingletonBase<CropManager>
{
    public TileMapCropsManager TileMapCropManger => cropsManager;
    [SerializeField] private TileMapCropsManager cropsManager;

    private bool isInitialized = false;

    public void SetCropsManager(TileMapCropsManager manager)
    {
        cropsManager = manager;
        if (!isInitialized)
        {
            cropsManager.Initialize();
            isInitialized = true;
        }
        else
        {
            cropsManager.InitilizeScene();
        }
    }
...
}
